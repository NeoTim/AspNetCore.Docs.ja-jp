---
title: 運用SignalR環境のホスティングとスケーリングの ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalRを使用するアプリのパフォーマンスとスケーリングに関する問題を回避する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 23ac2b1c80b9d73d6e9ac57f0ef774ac2ea54be4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775077"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR のホストとスケーリング

[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、

この記事では、ASP.NET Core SignalR を使用する高トラフィックアプリのホストとスケーリングに関する考慮事項について説明します。

## <a name="sticky-sessions"></a>固定セッション

SignalR を使用するには、特定の接続に対するすべての HTTP 要求を同じサーバープロセスで処理する必要があります。 SignalR がサーバーファーム (複数のサーバー) で実行されている場合は、"固定セッション" を使用する必要があります。 "固定セッション" は、一部のロードバランサーによってセッションアフィニティとも呼ばれます。 Azure App Service は、[アプリケーション要求ルーティング](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview)処理 (ARR) を使用して要求をルーティングします。 Azure App Service で "ARR Affinity" 設定を有効にすると、"固定セッション" が有効になります。 固定セッションが不要な状況は次のとおりです。

1. 単一のサーバーでホストする場合、1つのプロセスで実行します。
1. Azure SignalR サービスを使用する場合。
1. すべてのクライアントが Websocket**のみ**を使用するように構成され、 [skipnegotiation 設定](xref:signalr/configuration#configure-additional-options)がクライアント構成で有効に**なっている**場合。

その他のすべての状況 (Redis バックプレーンを使用する場合を含む) では、サーバー環境を固定セッション用に構成する必要があります。

SignalR の Azure App Service の構成に関するガイダンスに<xref:signalr/publish-to-azure-web-app>ついては、「」を参照してください。

## <a name="tcp-connection-resources"></a>TCP 接続リソース

Web サーバーがサポートできる同時 TCP 接続の数は制限されています。 標準 HTTP クライアントは、*一時的*な接続を使用します。 これらの接続は、クライアントがアイドル状態になったときに終了し、後で再度開くことができます。 一方、SignalR 接続は*永続的*です。 クライアントがアイドル状態になっても、SignalR 接続は開いたままになります。 多数のクライアントにサービスを提供する高トラフィックアプリでは、これらの永続的な接続により、サーバーが最大接続数に達する可能性があります。

また、固定接続では、各接続を追跡するために、追加のメモリがいくつか消費されます。

SignalR によって接続関連のリソースが多用されると、同じサーバー上でホストされている他の web アプリに影響を与える可能性があります。 SignalR が開いたときに、使用可能な最後の TCP 接続が保持されている場合、同じサーバー上の他の web アプリにも使用できる接続がありません。

サーバーの接続が不足している場合は、ランダムソケットエラーと接続リセットエラーが表示されます。 次に例を示します。

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

SignalR リソースの使用量が他の web アプリでエラーを発生させないようにするには、他の web アプリとは異なるサーバーで SignalR を実行します。

SignalR リソース使用量が SignalR アプリでエラーを発生させないようにするには、スケールアウトして、サーバーが処理する接続の数を制限します。

## <a name="scale-out"></a>スケール アウト

SignalR を使用するアプリでは、そのすべての接続を追跡する必要があります。これにより、サーバーファームに関する問題が発生します。 サーバーを追加すると、他のサーバーが認識していない新しい接続が取得されます。 たとえば、次の図の各サーバーの SignalR は、他のサーバー上の接続を認識していません。 いずれかのサーバーで SignalR がすべてのクライアントにメッセージを送信しようとすると、メッセージはそのサーバーに接続されているクライアントのみに送られます。

![バックプレーンを使用しない SignalR のスケーリング](scale/_static/scale-no-backplane.png)

この問題を解決するためのオプションは、 [Azure SignalR サービス](#azure-signalr-service)と[Redis バックプレーン](#redis-backplane)です。

## <a name="azure-signalr-service"></a>Azure SignalR Service

Azure SignalR サービスは、バックプレーンではなくプロキシです。 クライアントがサーバーへの接続を開始するたびに、クライアントはサービスに接続するためにリダイレクトされます。 このプロセスを次の図に示します。

![Azure SignalR サービスへの接続を確立する](scale/_static/azure-signalr-service-one-connection.png)

その結果、次の図に示すように、サービスはすべてのクライアント接続を管理しますが、各サーバーにはサービスへの接続数をごくわずかにする必要があります。

![サービスに接続されているクライアント、サービスに接続されているサーバー](scale/_static/azure-signalr-service-multiple-connections.png)

このスケールアウトのアプローチには、Redis バックプレーンよりもいくつかの利点があります。

* クライアント[アフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれる固定セッションは、接続時にクライアントが Azure SignalR サービスに直ちにリダイレクトされるため、必須ではありません。
* SignalR アプリは、送信されたメッセージの数に基づいてスケールアウトできます。一方、Azure SignalR サービスは、任意の数の接続を処理するように自動的にスケーリングします。 たとえば、クライアントが何千も存在する可能性はありますが、1秒あたり数個のメッセージだけが送信された場合、SignalR アプリは接続自体を処理するために、複数のサーバーにスケールアウトする必要がありません。
* SignalR アプリでは、SignalR のない web アプリよりもはるかに多くの接続リソースは使用されません。

これらの理由から、azure でホストされているすべての ASP.NET Core SignalR アプリ (App Service、Vm、コンテナーなど) に対して Azure SignalR サービスを使用することをお勧めします。

詳細については、 [Azure SignalR サービスのドキュメント](/azure/azure-signalr/signalr-overview)を参照してください。

## <a name="redis-backplane"></a>Redis バックプレーン

[Redis](https://redis.io/)は、パブリッシュ/サブスクライブモデルを持つメッセージングシステムをサポートするメモリ内キー値ストアです。 SignalR Redis バックプレーンは、pub/sub 機能を使用して、他のサーバーにメッセージを転送します。 クライアントが接続を確立すると、接続情報がバックプレーンに渡されます。 サーバーは、すべてのクライアントにメッセージを送信するときに、バックプレーンに送信します。 バックプレーンは、接続されているすべてのクライアントと、それらがあるサーバーを認識します。 このメッセージは、各サーバーを介してすべてのクライアントに送信されます。 このプロセスを次の図に示します。

![Redis バックプレーン、1つのサーバーからすべてのクライアントに送信されたメッセージ](scale/_static/redis-backplane.png)

Redis バックプレーンは、お客様のインフラストラクチャでホストされているアプリに推奨されるスケールアウトアプローチです。 データセンターと Azure データセンターの間に大きな接続の待機時間がある場合、低待機時間または高いスループットの要件を持つオンプレミスのアプリでは、Azure SignalR サービスが実用的な選択肢ではない可能性があります。

前述した Azure SignalR サービスの利点は、Redis バックプレーンの欠点です。

* 次の**両方**が当てはまる場合を除き、固定セッション ([クライアントアフィニティ](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)とも呼ばれます) が必要です。
  * すべてのクライアントは、Websocket**のみ**を使用するように構成されています。
  * クライアント構成で[Skipnegotiation 設定](xref:signalr/configuration#configure-additional-options)が有効になっています。 
   サーバーで接続が開始されると、接続はそのサーバー上にとどまります。
* アプリSignalRは、送信されるメッセージが少ない場合でも、クライアントの数に基づいてスケールアウトする必要があります。
* アプリSignalRは、を使用しないSignalR場合、web アプリよりもはるかに多くの接続リソースを使用します。

## <a name="iis-limitations-on-windows-client-os"></a>Windows クライアント OS での IIS の制限事項

Windows 10 と Windows 8.x はクライアントオペレーティングシステムです。 クライアントオペレーティングシステムの IIS では、同時接続数が10個に制限されています。 SignalRの接続は次のとおりです。

* 一時的で、頻繁に再確立されます。
* 使用されなくなった場合、すぐに**は破棄されません**。

上記の条件を満たすと、クライアント OS で10個の接続制限に達する可能性が高くなります。 クライアント OS を開発に使用する場合は、次のことをお勧めします。

* IIS を避けます。
* 配置ターゲットとして Kestrel または IIS Express を使用します。

## <a name="linux-with-nginx"></a>Nginx を使用した Linux

Websocket の`Connection` `Upgrade` SignalRプロキシとヘッダーを次のように設定します。

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

詳細については、「[WebSocket プロキシとしての NGINX](https://www.nginx.com/blog/websocket-nginx/)」を参照してください。

## <a name="third-party-signalr-backplane-providers"></a>サードパーティ製SignalRバックプレーンプロバイダー

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

* [Azure SignalRサービスのドキュメント](/azure/azure-signalr/signalr-overview)
* [Redis バックプレーンを設定する](xref:signalr/redis-backplane)
