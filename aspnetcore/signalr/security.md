---
title: ASP.NET Core SignalR でのセキュリティに関する考慮事項
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f92b56132d0fa55665568416d0760430cb698f8b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655154"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR でのセキュリティに関する考慮事項

By [Andrew Stanton-看護師](https://twitter.com/anurse)

この記事では、SignalRのセキュリティ保護について説明します。

## <a name="cross-origin-resource-sharing"></a>クロス オリジン リソース共有

[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を使用して、ブラウザーでのクロスオリジン SignalR 接続を許可することができます。 JavaScript コードが SignalR アプリとは別のドメインでホストされている場合、JavaScript が SignalR アプリに接続できるようにするには、 [CORS ミドルウェア](xref:security/cors)を有効にする必要があります。 信頼または制御するドメインからのクロスオリジン要求を許可します。 例 :

* サイトは `http://www.example.com` でホストされています
* SignalR アプリは `http://signalr.example.com` でホストされています

SignalR アプリで CORS を構成して、オリジン `www.example.com`のみを許可する必要があります。

CORS の構成の詳細については、「[クロスオリジン要求 (cors) を有効にする](xref:security/cors)」を参照してください。 SignalR には、次の CORS ポリシー**が必要**です。

* 想定される特定のオリジンを許可します。 配信元を許可することは可能ですが、安全でも推奨され**ません**。
* HTTP メソッド `GET` と `POST` は許可されている必要があります。
* Cookie ベースの固定セッションが正常に機能するためには、資格情報を許可する必要があります。 認証が使用されていない場合でも、有効にする必要があります。

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/dotnet/AspNetCore.Docs/issues/16003
.-->

たとえば、次の CORS ポリシーを使用すると、`https://example.com` でホストされている SignalR ブラウザークライアントは、`https://signalr.example.com`でホストされている SignalR アプリにアクセスできます。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR は、Azure App Service の組み込みの CORS 機能と互換性がありません。

## <a name="websocket-origin-restriction"></a>WebSocket 配信元の制限

::: moniker range=">= aspnetcore-2.2"

CORS で提供される保護は、WebSocket には適用されません。 Websocket の配信元の制限については、「 [websocket の配信元の制限](xref:fundamentals/websockets#websocket-origin-restriction)」を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS で提供される保護は、WebSocket には適用されません。 ブラウザーでは以下を実行**しません**。

* CORS の事前要求を実行する。
* WebSocket 要求を行うときに `Access-Control` ヘッダーに指定された制限を考慮する。

ただし、WebSocket 要求を発行するときにはブラウザーから `Origin` ヘッダーが送信されます。 予期した配信元からの WebSocket のみが許可されるように、アプリケーションでこれらのヘッダーが検証されるように構成する必要があります。

ASP.NET Core 2.1 以降では、 **`UseSignalR`前に**配置されたカスタムミドルウェアと `Configure`の認証ミドルウェアを使用して、ヘッダーの検証を行うことができます。

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin` ヘッダーは、クライアントによって制御され、`Referer` のように偽装することができます。 これらのヘッダーは、認証メカニズムとして使用**しない**でください。

::: moniker-end

## <a name="connectionid"></a>ConnectionId

`ConnectionId` を公開すると、SignalR サーバーまたはクライアントのバージョンが2.2 以前 ASP.NET Core 場合は、悪意のある偽装が発生する可能性があります。 SignalR サーバーとクライアントのバージョンが3.0 以降 ASP.NET Core 場合は、`ConnectionId` ではなく `ConnectionToken` をシークレットに保持する必要があります。 `ConnectionToken` は、どの API でも意図的に公開されていません。  古い SignalR クライアントがサーバーに接続していないことを確認するのは困難な場合があります。したがって、SignalR サーバーのバージョンが3.0 以降 ASP.NET Core 場合でも、`ConnectionId` は公開されません。

## <a name="access-token-logging"></a>アクセストークンのログ記録

Websocket またはサーバー送信イベントを使用する場合、ブラウザークライアントはクエリ文字列にアクセストークンを送信します。 一般に、クエリ文字列を使用したアクセストークンの受信は、標準の `Authorization` ヘッダーを使用する場合と同じように行われます。 クライアントとサーバー間のセキュリティで保護されたエンドツーエンド接続を確保するには、常に HTTPS を使用します。 多くの web サーバーでは、クエリ文字列を含め、各要求の URL がログに記録されます。 Url をログに記録すると、アクセストークンがログに記録される場合があります。 では、各要求の URL が既定でログに記録されます。これには、クエリ文字列が含まれます。 ASP.NET Core 例 :

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

このデータをサーバーログに記録することに懸念がある場合は、`Microsoft.AspNetCore.Hosting` logger を `Warning` レベル以上に構成することで、このログ記録を完全に無効にすることができます (これらのメッセージは `Info` レベルで書き込まれます)。 詳細については、「[ログのフィルター処理](xref:fundamentals/logging/index#log-filtering)」を参照してください。 それでも特定の要求情報をログに記録する場合は、必要なデータをログに記録する[ミドルウェアを記述](xref:fundamentals/middleware/write)し、`access_token` クエリ文字列値 (存在する場合) を除外することができます。

## <a name="exceptions"></a>例外

例外メッセージは、通常、クライアントに公開されない機密データと見なされます。 既定では、SignalR は、ハブメソッドによってスローされた例外の詳細をクライアントに送信しません。 代わりに、エラーが発生したことを示す一般的なメッセージをクライアントが受信します。 クライアントへの例外メッセージの配信は、 [EnableDetailedErrors](xref:signalr/configuration#configure-server-options)を使用して (開発やテストなどで) オーバーライドできます。 例外メッセージは、実稼働アプリでクライアントに公開しないでください。

## <a name="buffer-management"></a>バッファー管理

SignalR は、接続ごとのバッファーを使用して、受信メッセージと送信メッセージを管理します。 既定では、SignalR によってこれらのバッファーは 32 KB に制限されます。 クライアントまたはサーバーが送信できる最大メッセージサイズは 32 KB です。 メッセージの接続で消費される最大メモリは 32 KB です。 メッセージが常に 32 KB より小さい場合は、次の制限を減らすことができます。

* クライアントが大きなメッセージを送信できないようにします。
* サーバーは、メッセージを受け入れるために大きなバッファーを割り当てる必要はありません。

メッセージが 32 KB を超える場合は、制限を増やすことができます。 この制限を引き上げると、次のようになります。

* クライアントは、サーバーが大きなメモリバッファーを割り当てる可能性があります。
* 大きなバッファーをサーバーに割り当てると、同時接続の数が減少する可能性があります。

受信メッセージと送信メッセージには制限があり、どちらも `MapHub`で構成された[HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options)オブジェクトで構成できます。

* `ApplicationMaxBufferSize` は、サーバーがバッファーするクライアントからの最大バイト数を表します。 クライアントがこの制限を超えるメッセージを送信しようとすると、接続が閉じられる場合があります。
* `TransportMaxBufferSize` は、サーバーが送信できる最大バイト数を表します。 サーバーがこの制限を超えるメッセージ (ハブメソッドからの戻り値を含む) を送信しようとすると、例外がスローされます。

制限を `0` に設定すると、制限が無効になります。 制限を削除すると、クライアントは任意のサイズのメッセージを送信できます。 サイズの大きいメッセージを送信する悪意のあるクライアントは、余分なメモリが割り当てられる可能性があります。 メモリ使用量が多すぎると、同時接続の数が大幅に減少します。
