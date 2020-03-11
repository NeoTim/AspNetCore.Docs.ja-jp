---
title: ASP.NET Core には SignalR の MessagePack ハブプロトコルを使用します。
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core SignalRに追加します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654566"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>ASP.NET Core には SignalR の MessagePack ハブプロトコルを使用します。

[Brennan Conroy](https://github.com/BrennanConroy)

この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。

## <a name="what-is-messagepack"></a>MessagePack とは

[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。 これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。 バイナリ形式であるため、メッセージが MessagePack パーサーによって渡されない限り、ネットワークトレースとログを確認するときにメッセージを読み取ることはできません。 SignalR には MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。

## <a name="configure-messagepack-on-the-server"></a>サーバーでの MessagePack の構成

サーバーで MessagePack ハブプロトコルを有効にするには、アプリに `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` パッケージをインストールします。 Startup.cs ファイルで、`AddSignalR` 呼び出しに `AddMessagePackProtocol` を追加して、サーバーで MessagePack のサポートを有効にします。

> [!NOTE]
> JSON は既定で有効になっています。 MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack によるデータの書式設定方法をカスタマイズするために、`AddMessagePackProtocol` ではオプションを構成するためのデリゲートが使用されます。 このデリゲートでは、`FormatterResolvers` プロパティを使用して、MessagePack のシリアル化オプションを構成できます。 リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。 属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。 たとえば、`MessagePackSecurity.Active` 静的プロパティを `MessagePackSecurity.UntrustedData`に設定します。 `MessagePackSecurity.Active` を設定するには、[バージョンの MessagePack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。 `MessagePack` 1.9. x をインストールすると SignalR が使用するバージョンがアップグレードされます。 `MessagePackSecurity.Active` が `MessagePackSecurity.UntrustedData`に設定されていない場合、悪意のあるクライアントによってサービス拒否が発生する可能性があります。 次のコードに示すように `Program.Main`で `MessagePackSecurity.Active` を設定します。

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>クライアントでの MessagePack の構成

> [!NOTE]
> JSON は、サポートされているクライアントに対して既定で有効になっています。 クライアントは、1つのプロトコルのみをサポートできます。 MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。

### <a name="net-client"></a>.NET クライアント

.NET クライアントで MessagePack を有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack` パッケージをインストールし、`HubConnectionBuilder`で `AddMessagePackProtocol` を呼び出します。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この `AddMessagePackProtocol` の呼び出しでは、サーバーと同じようにオプションを構成するためのデリゲートが使用されます。

### <a name="javascript-client"></a>JavaScript クライアント

::: moniker range=">= aspnetcore-3.0"

JavaScript クライアントの MessagePack のサポートは、`@microsoft/signalr-protocol-msgpack` npm パッケージによって提供されます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

JavaScript クライアントの MessagePack のサポートは、`@aspnet/signalr-protocol-msgpack` npm パッケージによって提供されます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。

::: moniker range=">= aspnetcore-3.0"

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

ブラウザーでは、`msgpack5` ライブラリも参照する必要があります。 参照を作成するには、`<script>` タグを使用します。 ライブラリは node_modules にあります。- *msgpack5-dist-msgpack5js*

> [!NOTE]
> `<script>` 要素を使用する場合、順序は重要です。 *Msgpack5*の前に*signalr-protocol-msgpack*が参照されている場合、messagepack に接続しようとするとエラーが発生します。 *signalr*は、 *signalr-protocol-msgpack*の前にも必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`HubConnectionBuilder` に `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` を追加すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>MessagePack の特性

MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。

### <a name="messagepack-is-case-sensitive"></a>MessagePack では大文字と小文字が区別されます

MessagePack プロトコルでは大文字と小文字が区別されます。 たとえば、次C#のクラスについて考えてみます。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript クライアントから送信する場合は、`PascalCased` プロパティ名を使用する必要があります。これC#は、大文字と小文字がクラスに正確に一致する必要があるためです。 例 :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased` 名を使用しても、 C#クラスに正しくバインドされません。 この問題を回避するには、`Key` 属性を使用して、MessagePack プロパティに別の名前を指定します。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind はシリアル化/逆シリアル化時に保持されません

MessagePack プロトコルは、`DateTime`の `Kind` 値をエンコードする手段を提供していません。 その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。 `DateTime` 値を現地時刻で使用している場合は、送信する前に UTC に変換することをお勧めします。 これらを受信時に UTC からローカル時刻に変換します。

この制限の詳細については、「GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue は、JavaScript の MessagePack ではサポートされていません

SignalR JavaScript クライアントによって使用されている[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、messagepack の `timestamp96` の種類をサポートしていません。 この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。 `DateTime.MinValue` の値は `January 1, 0001` `timestamp96` 値でエンコードする必要があります。 このため、JavaScript クライアントへの `DateTime.MinValue` の送信はサポートされていません。 `DateTime.MinValue` が JavaScript クライアントによって受信されると、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、`DateTime.MinValue` は、"missing" または `null` 値をエンコードするために使用されます。 MessagePack でその値をエンコードする必要がある場合は、null 許容の `DateTime` 値 (`DateTime?`) を使用するか、日付が存在するかどうかを示す別の `bool` 値をエンコードします。

この制限の詳細については、「GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前" コンパイル環境での MessagePack のサポート

.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8)ライブラリは、コード生成を使用してシリアル化を最適化します。 その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザー コードを "事前に生成する" ことで MessagePack を使用することができます。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports)を参照してください。 シリアライザーを事前に生成したら、`AddMessagePackProtocol`に渡される構成デリゲートを使用してそれらを登録できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack での型チェックの方が厳しい

JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信したオブジェクトのプロパティ値が数値 (`{ foo: 42 }`) であっても、.NET クラスのプロパティの型が `string`である場合、値は変換されます。 ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連リソース

* [開始するには](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)
