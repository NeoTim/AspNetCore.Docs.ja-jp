---
title: SignalRJava クライアントの ASP.NET Core
author: mikaelm12
description: ASP.NET Core Java クライアントの使用方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 5351bcf5adb4a79efc993f48acbb959f113c814f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634957"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>SignalRJava クライアントの ASP.NET Core

作成者: [Mikael Mengistu](https://twitter.com/MikaelM_12)

Java クライアントを使用すると、 SignalR Android アプリを含む java コードから ASP.NET Core サーバーに接続できます。 [JavaScript クライアント](xref:signalr/javascript-client)や[.net クライアント](xref:signalr/dotnet-client)と同様に、Java クライアントを使用すると、メッセージを受信してハブにリアルタイムで送信することができます。 Java クライアントは ASP.NET Core 2.2 以降で使用できます。

この記事で参照されているサンプルの Java コンソールアプリは、java クライアントを使用し SignalR ます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-no-locsignalr-java-client-package"></a>SignalRJava クライアントパッケージをインストールする

*Signalr*の JAR ファイルを使用すると、クライアントはハブに接続でき SignalR ます。 最新の JAR ファイルのバージョン番号を確認するには、 [Maven の検索結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)を参照してください。

Gradle を使用する場合は、 `dependencies` *Gradle* ファイルのセクションに次の行を追加します。

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven を使用する場合は、 `<dependencies>` *pom.xml* ファイルの要素内に次の行を追加します。

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>ハブへの接続

を確立するには、を `HubConnection` `HubConnectionBuilder` 使用する必要があります。 ハブの URL とログレベルは、接続の作成時に構成できます。 前のメソッドのいずれかを呼び出すことによって、必要なオプションを構成 `HubConnectionBuilder` `build` します。 との接続を開始 `start` します。

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>クライアントからのハブメソッドの呼び出し

を呼び出すと、 `send` ハブメソッドが呼び出されます。 ハブメソッドの名前と、ハブメソッドで定義されているすべての引数をに渡し `send` ます。

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> クライアントからのハブメソッドの呼び出しは、Azure SignalR サービスを *既定* のモードで使用している場合にのみサポートされます。 詳細については、「 [よく寄せられる質問 (azure Signalr GitHub リポジトリ)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)」を参照してください。

## <a name="call-client-methods-from-hub"></a>ハブからクライアントメソッドを呼び出す

`hubConnection.on`ハブが呼び出すことができるクライアント上のメソッドを定義するには、を使用します。 ビルドの後、接続を開始する前に、メソッドを定義します。

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>ログ記録の追加

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

## <a name="android-development-notes"></a>Android 開発ノート

クライアント機能の Android SDK 互換性については SignalR 、ターゲット Android SDK バージョンを指定するときに、次の項目を考慮してください。

* SignalRJava クライアントは、ANDROID API レベル16以降で実行されます。
* Azure サービスでは SignalR TLS 1.2 が必要であり、SHA-1 ベース [の SignalR ](/azure/azure-signalr/signalr-overview) 暗号スイートはサポートされていないため、azure サービスを介して接続するには Android API レベル20以降が必要になります。 Android では、API レベル20で [SHA-256 (およびそれ以降) の暗号スイートのサポートが追加されました](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 。

## <a name="configure-bearer-token-authentication"></a>ベアラートークン認証を構成する

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)に "アクセストークンファクトリ" を提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>既知の制限事項

::: moniker range=">= aspnetcore-3.0"

* JSON プロトコルのみがサポートされています。
* トランスポートフォールバックおよびサーバー送信イベントトランスポートはサポートされていません。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* JSON プロトコルのみがサポートされています。
* Websocket トランスポートのみがサポートされています。
* ストリーミングはまだサポートされていません。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [Java API リファレンス](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
