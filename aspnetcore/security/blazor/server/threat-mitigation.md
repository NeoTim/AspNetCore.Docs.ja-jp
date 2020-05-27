---
タイトル: ' ASP.NET Core サーバーの脅威軽減ガイダンス Blazor : 説明: ' サーバーアプリに対するセキュリティ上の脅威を軽減する方法について説明 Blazor します。 '
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>ASP.NET Core サーバーの脅威軽減のガイダンス Blazor

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

Blazorサーバーアプリは、*ステートフル*なデータ処理モデルを採用しています。このモデルでは、サーバーとクライアントが長期間の関係を維持します。 永続的な状態は回線によって維持されます。[回線](xref:blazor/state-management)は、有効期間が長い可能性もある接続にまたがる場合があります。

ユーザーがサーバーサイトにアクセスすると、サーバー Blazor のメモリに回線が作成されます。 回線は、ユーザーが UI のボタンを選択したときなど、どのコンテンツをレンダリングしてイベントに応答するかをブラウザーに示します。 これらのアクションを実行するために、回線は、サーバー上のユーザーのブラウザーと .NET メソッドで JavaScript 関数を呼び出します。 この双方向の JavaScript ベースの対話は、 [javascript interop (JS interop)](xref:blazor/call-javascript-from-dotnet)と呼ばれます。

JS 相互運用はインターネット経由で行われ、クライアントはリモートブラウザーを使用するため、 Blazor サーバーアプリはほとんどの web アプリのセキュリティに関する懸念事項を共有します。 このトピックでは、サーバーアプリに対する一般的な脅威について説明 Blazor し、インターネットに接続するアプリに重点を置いた脅威軽減のガイダンスを提供します。

企業ネットワークやイントラネット内などの制約された環境では、次のいずれかの軽減ガイダンスがあります。

* は、制約された環境には適用されません。
* 制約された環境でセキュリティリスクが低いため、を実装する価値はありません。

## <a name="blazor-and-shared-state"></a>Blazor と共有状態

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>リソース枯渇

リソース枯渇は、クライアントがサーバーと通信し、サーバーが過剰なリソースを消費する場合に発生する可能性があります。 過剰なリソース消費は主に次のように影響します。

* [CPU](#cpu)
* [[メモリ]](#memory)
* [クライアント接続](#client-connections)

サービス拒否 (DoS) 攻撃は、通常、アプリまたはサーバーのリソースを枯渇させることを求めています。 ただし、リソース枯渇はシステムへの攻撃の結果であるとは限りません。 たとえば、ユーザーの要求が多いため、有限のリソースが使い果たされる可能性があります。 DoS の詳細については、「[サービス拒否 (dos) 攻撃](#denial-of-service-dos-attacks)」セクションを参照してください。

Blazorデータベースやファイルハンドル (ファイルの読み取りと書き込みに使用される) など、フレームワークの外部にあるリソースも、リソースの枯渇に直面することがあります。 詳細については、「<xref:performance/performance-best-practices>」を参照してください。

### <a name="cpu"></a>CPU

CPU の枯渇は、1つまたは複数のクライアントが大量の CPU 作業を実行するようサーバーに強制した場合に発生する可能性があります。

たとえば、 Blazor *Fibonnacci 数*を計算するサーバーアプリを考えてみます。 Fibonnacci 番号は Fibonnacci シーケンスから生成されます。シーケンス内の各数値は、前の2つの数値の合計です。 回答に必要な作業量は、シーケンスの長さと初期値のサイズによって異なります。 アプリがクライアントの要求に制限を設けない場合、CPU 負荷の高い計算で CPU の時間が消費され、他のタスクのパフォーマンスが低下する可能性があります。 過剰なリソース消費は、可用性に影響を与えるセキュリティ上の懸念事項です。

CPU 枯渇は、すべての公開アプリに関する問題です。 通常の web アプリでは、要求と接続は安全としてタイムアウトに Blazor なりますが、サーバーアプリでは同じセーフガードが提供されません。 Blazorサーバーアプリには、CPU を集中的に使用する作業を実行する前に、適切なチェックと制限を含める必要があります。

### <a name="memory"></a>メモリ

メモリ不足は、1つまたは複数のクライアントがサーバーに大量のメモリを強制的に使用する場合に発生する可能性があります。

たとえば、 Blazor 項目のリストを受け入れて表示するコンポーネントがある-サーバー側アプリを考えてみます。 Blazor許容される項目数またはクライアントに表示される項目数に制限がない場合は、メモリを集中的に使用する処理とレンダリングによって、サーバーのパフォーマンスが低下するポイントまでサーバーのメモリが消費される可能性があります。 サーバーがクラッシュした場合、クラッシュしたと思われる場合があります。

サーバー上のメモリ不足のシナリオに関連する項目の一覧を保持および表示するには、次のシナリオを検討してください。

* プロパティまたはフィールド内の項目は、 `List<MyItem>` サーバーのメモリを使用します。 アプリで項目の一覧を無制限に拡張できる場合は、サーバーでメモリが不足する危険性があります。 メモリが不足すると、現在のセッションが終了 (クラッシュ) し、そのサーバーインスタンス内のすべての同時実行セッションでメモリ不足の例外が発生します。 このシナリオが発生しないようにするには、同時実行ユーザーにアイテムの制限を課すデータ構造をアプリで使用する必要があります。
* ページング方式がレンダリングに使用されていない場合、サーバーは、UI に表示されないオブジェクトに対して追加のメモリを使用します。 項目の数に制限がないと、メモリの需要によって使用可能なサーバーメモリが枯渇する可能性があります。 このシナリオを回避するには、次のいずれかの方法を使用します。
  * レンダリング時に改ページ調整されたリストを使用します。
  * 最初の 100 ~ 1000 項目のみを表示し、表示された項目以外の項目を検索するために検索条件を入力するようユーザーに要求します。
  * より高度なレンダリングシナリオでは、*仮想化*をサポートするリストまたはグリッドを実装します。 仮想化を使用すると、一覧に表示されるのは、現在ユーザーに表示されている項目のサブセットのみです。 UI でユーザーがスクロールバーを操作すると、コンポーネントは表示に必要な項目だけをレンダリングします。 現在、表示に必要とされていない項目は、セカンダリストレージに保持できます。これは理想的な方法です。 記憶されていない項目をメモリに保持することもできますが、これはあまり理想的ではありません。

Blazorサーバーアプリは、WPF、Windows フォーム、webassembly、ステートフルアプリ用の他の UI フレームワークと同様のプログラミングモデルを提供 Blazor します。 主な違いは、いくつかの UI フレームワークでは、アプリによって消費されるメモリがクライアントに属し、その個々のクライアントのみに影響することです。 たとえば、 Blazor webassembly はクライアント上で完全に実行され、クライアントメモリリソースのみを使用します。 サーバーシナリオでは、 Blazor アプリによって消費されるメモリはサーバーに属し、サーバーインスタンス上のクライアント間で共有されます。

サーバー側のメモリ要求は、すべてのサーバーアプリで考慮され Blazor ます。 ただし、ほとんどの web アプリはステートレスであり、応答が返されると、要求の処理中に使用されるメモリが解放されます。 一般的な推奨事項として、クライアント接続を保持する他のサーバー側アプリと同じように、クライアントが非バインドメモリを割り当てられないようにすることをお勧めします。 サーバーアプリによって消費されるメモリは、 Blazor 1 つの要求よりも長い時間保持されます。

> [!NOTE]
> 開発時には、プロファイラーを使用したり、トレースをキャプチャしてクライアントのメモリ要求を評価したりできます。 プロファイラーまたはトレースは、特定のクライアントに割り当てられたメモリをキャプチャしません。 開発時に特定のクライアントのメモリ使用量をキャプチャするには、ダンプをキャプチャし、ユーザーの回線をルートとするすべてのオブジェクトのメモリ要求を調べます。

### <a name="client-connections"></a>クライアント接続

接続の枯渇は、1つまたは複数のクライアントがサーバーへの同時接続を多数開いていて、他のクライアントが新しい接続を確立できない場合に発生する可能性があります。

Blazorクライアントは、セッションごとに1つの接続を確立し、ブラウザーウィンドウが開いている間は接続を開いたままにします。 すべての接続を維持するサーバーでの要求は、アプリに固有ではありません Blazor 。 サーバーアプリの接続とステートフルな性質によって Blazor 、接続の枯渇はアプリの可用性に対するリスクが高くなります。

既定では、サーバーアプリのユーザー1人あたりの接続数に制限はありません Blazor 。 アプリに接続制限が必要な場合は、次の方法の1つまたは複数を実行します。

* 認証が必要です。これにより、承認されていないユーザーがアプリに接続する機能が自然に制限されます。 このシナリオを効果的にするには、ユーザーが新しいユーザーをにプロビジョニングできないようにする必要があります。
* ユーザーあたりの接続数を制限します。 接続の制限は、次の方法で実現できます。 正当なユーザーにアプリへのアクセスを許可します (たとえば、クライアントの IP アドレスに基づいて接続の制限が確立されている場合など)。
  * アプリレベル:
    * エンドポイントルーティングの拡張性。
    * アプリに接続し、ユーザーごとにアクティブなセッションを追跡するには、認証が必要です。
    * 制限に達したときに新しいセッションを拒否します。
    * プロキシを使用したアプリへのプロキシ WebSocket 接続 (クライアントからアプリへの接続を多重にする[Azure SignalR サービス](/azure/azure-signalr/signalr-overview)など)。 これにより、1台のクライアントが確立できるよりも多くの接続容量を備えたアプリが提供されるため、クライアントはサーバーへの接続を使い果たすことができません。
  * サーバーレベル: アプリの前にプロキシ/ゲートウェイを使用します。 たとえば、 [Azure のフロントドア](/azure/frontdoor/front-door-overview)を使用すると、アプリへの web トラフィックのグローバルルーティングを定義、管理、および監視することができます。

## <a name="denial-of-service-dos-attacks"></a>サービス拒否 (DoS) 攻撃

サービス拒否 (DoS) 攻撃では、クライアントによってサーバーが1つ以上のリソースを消費し、アプリを使用できなくなります。 Blazorサーバーアプリには既定の制限がいくつか含まれており、 SignalR で設定されている DoS 攻撃から保護するために、他の ASP.NET Core や制限に依存して <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> います。

| Blazorサーバーアプリの制限 | 説明 | Default |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 特定のサーバーが一度にメモリに保持している切断された回線の最大数。 | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 切断された回線がメモリに保持されてから破棄されるまでの最大時間。 | 3 分 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 非同期の JavaScript 関数呼び出しがタイムアウトするまでにサーバーが待機する最大時間。 | 1 分 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 信頼性の高い再接続をサポートするために、サーバーが1回線あたりのメモリに保持する未確認のレンダーバッチの最大数。 制限に達すると、クライアントによって1つ以上のバッチが確認されるまで、サーバーは新しいレンダーバッチの生成を停止します。 | 10 |

単一の受信ハブメッセージの最大メッセージサイズをに設定 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> します。

| SignalRおよび ASP.NET Core の制限 | 説明 | Default |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | 個々のメッセージのメッセージサイズ。 | 32 KB |

## <a name="interactions-with-the-browser-client"></a>ブラウザー (クライアント) との対話

クライアントは、JS 相互運用イベントのディスパッチとレンダリングの完了を通じてサーバーと対話します。 JS 相互運用通信は、JavaScript と .NET の両方の方法で行われます。

* ブラウザーイベントは、非同期方式でクライアントからサーバーにディスパッチされます。
* サーバーは、必要に応じて UI を非同期に応答します。

### <a name="javascript-functions-invoked-from-net"></a>.NET から呼び出される JavaScript 関数

.NET メソッドから JavaScript への呼び出しの場合:

* すべての呼び出しには、失敗してから呼び出し元に返す、構成可能なタイムアウトがあり <xref:System.OperationCanceledException> ます。
  * 呼び出し () の既定のタイムアウトは <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> 1 分です。 この制限を構成するには、「」を参照してください <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> 。
  * キャンセルトークンを指定して、呼び出しごとに取り消しを制御できます。 キャンセルトークンが指定されている場合は、可能な限り既定の呼び出しタイムアウトを使用し、クライアントへの呼び出しに時間を制限します。
* JavaScript 呼び出しの結果を信頼することはできません。 Blazorブラウザーで実行されているアプリクライアントは、呼び出す JavaScript 関数を検索します。 関数が呼び出され、結果またはエラーが生成されます。 悪意のあるクライアントは次のことを試みることができます。
  * JavaScript 関数からエラーを返すことによって、アプリの問題を発生させます。
  * JavaScript 関数から予期しない結果を返すことによって、サーバーで意図しない動作を発生させます。

次の予防措置を講じて、上記のシナリオを防止します。

* 呼び出し中に発生する可能性のあるエラーを考慮するために、 [try catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメント内に JS 相互運用機能呼び出しをラップします。 詳細については、「<xref:blazor/handle-errors#javascript-interop>」を参照してください。
* アクションを実行する前に、JS 相互運用機能の呼び出しから返されたデータを検証します (エラーメッセージを含む)。

### <a name="net-methods-invoked-from-the-browser"></a>ブラウザーから呼び出される .NET メソッド

JavaScript から .NET メソッドへの呼び出しを信頼しません。 .NET メソッドが JavaScript に公開されている場合は、.NET メソッドの呼び出し方法を検討してください。

* アプリケーションのパブリックエンドポイントと同様に、JavaScript に公開されているすべての .NET メソッドを処理します。
  * 入力を検証します。
    * 値が想定される範囲内であることを確認します。
    * ユーザーが要求された操作を実行するアクセス許可を持っていることを確認します。
  * .NET メソッドの呼び出しの一部として、過剰な量のリソースを割り当てないでください。 たとえば、チェックを実行し、CPU とメモリの使用量に制限を設けます。
  * 静的メソッドとインスタンスメソッドを JavaScript クライアントに公開できることを考慮してください。 設計が適切な制約で状態の共有を行う場合を除き、セッション間で状態を共有することは避けてください。
    * `DotNetReference`最初に依存関係の挿入 (DI) によって作成されたオブジェクトを介して公開されるメソッドの場合、オブジェクトはスコープ付きオブジェクトとして登録する必要があります。 これは、サーバーアプリが使用する DI サービスに適用さ Blazor れます。
    * 静的メソッドの場合は、アプリがサーバーインスタンス上のすべてのユーザーを対象として状態を明示的に共有している場合を除き、クライアントにスコープを設定できない状態を確立しないようにします。
  * ユーザーが指定したデータをパラメーターで JavaScript 呼び出しに渡すことは避けてください。 パラメーターにデータを渡す必要がある場合は、JavaScript コードが[クロスサイトスクリプティング (XSS)](#cross-site-scripting-xss)の脆弱性を導入せずにデータを渡すことを確認してください。 たとえば、要素のプロパティを設定することによって、ユーザー指定のデータをドキュメントオブジェクトモデル (DOM) に書き込まないで `innerHTML` ください。 [コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)を使用して `eval` 、およびその他の安全でない JavaScript プリミティブを無効にすることを検討してください。
* フレームワークのディスパッチ実装の上に .NET 呼び出しのカスタムディスパッチを実装しないようにします。 ブラウザーへの .NET メソッドの公開は高度なシナリオであり、一般的な開発では推奨されません Blazor 。

### <a name="events"></a>events

イベントは、サーバーアプリへのエントリポイントを提供 Blazor します。 Web アプリでエンドポイントを保護する場合と同じ規則が、サーバーアプリのイベント処理に適用され Blazor ます。 悪意のあるクライアントは、イベントのペイロードとして送信するデータを送信できます。

次に例を示します。

* の変更イベントは、 `<select>` アプリがクライアントに提示したオプションに含まれていない値を送信する可能性があります。
* は、 `<input>` クライアント側の検証をバイパスして、テキストデータをサーバーに送信することができます。

アプリは、アプリが処理するイベントのデータを検証する必要があります。 Blazorフレームワーク[フォームコンポーネント](xref:blazor/forms-validation)は、基本検証を実行します。 アプリでカスタムフォームコンポーネントを使用する場合は、必要に応じてカスタムコードを記述してイベントデータを検証する必要があります。

Blazorサーバーイベントは非同期であるため、アプリケーションが新しいレンダリングを生成することによって反応するまでに、複数のイベントをサーバーにディスパッチすることができます。 これには、考慮すべきセキュリティ上の影響があります。 アプリでのクライアントアクションの制限は、現在表示されているビューステートに依存するのではなく、イベントハンドラー内で実行する必要があります。

ユーザーがカウンターを最大3回インクリメントできるようにするカウンターコンポーネントを考えてみます。 カウンターをインクリメントするボタンは、の値に基づいて条件付きで指定し `count` ます。

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

クライアントは、フレームワークがこのコンポーネントの新しいレンダリングを生成する前に、1つまたは複数のインクリメントイベントをディスパッチできます。 結果として、は `count` UI によって短時間で削除されないので、ユーザーが*3 回以上*インクリメントできることになります。 次の例では、3つのインクリメントの制限を達成するための正しい方法を `count` 示しています。

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

ハンドラー内にチェックを追加することにより `if (count < 3) { ... }` 、現在のアプリの状態に基づいてインクリメントするかどうかを決定し `count` ます。 この決定は、前の例とは異なり、一時的に古くなっている可能性がある UI の状態に基づいていません。

### <a name="guard-against-multiple-dispatches"></a>複数のディスパッチに対して保護する

イベントコールバックが、外部サービスまたはデータベースからのデータのフェッチなど、長時間実行される操作を非同期に呼び出す場合は、ガードの使用を検討してください。 ガードを使用すると、操作の進行中に視覚的なフィードバックが発生している間に、ユーザーが複数の操作をキューに入れることを防ぐことができます。 次のコンポーネントコードは、 `isLoading` が `true` サーバーからデータを取得するときにをに設定し `GetForecastAsync` ます。 `isLoading`はですが `true` 、UI ではボタンが無効になっています。

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

前の例で示されているガードパターンは、バックグラウンド操作がパターンで非同期に実行された場合に機能し `async` - `await` ます。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>早期にキャンセルして、dispose を使用しないようにする

「[複数のディスパッチに対するガード](#guard-against-multiple-dispatches)」で説明されているように、ガードを使用するだけでなく、コンポーネントが破棄されたときに、を使用して <xref:System.Threading.CancellationToken> 実行時間の長い操作をキャンセルすることを検討してください。 このアプローチには、コンポーネントでの*dispose の使用*を回避するという追加の利点があります。

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>大量のデータを生成するイベントを回避する

やなどの一部の DOM イベントは、 `oninput` `onscroll` 大量のデータを生成できます。 サーバーアプリでこれらのイベントを使用することは避けて Blazor ください。

## <a name="additional-security-guidance"></a>セキュリティに関するその他のガイダンス

ASP.NET Core アプリをセキュリティで保護するためのガイダンスは、 Blazor サーバーアプリに適用されます。詳細については、次のセクションで説明します。

* [ログ記録と機微なデータ](#logging-and-sensitive-data)
* [HTTPS を使用した転送中の情報の保護](#protect-information-in-transit-with-https)
* [クロスサイトスクリプティング (XSS)](#cross-site-scripting-xss)
* [クロスオリジン保護](#cross-origin-protection)
* [[-Jacking] をクリックします。](#click-jacking)
* [リダイレクトを開く](#open-redirects)

### <a name="logging-and-sensitive-data"></a>ログ記録と機微なデータ

クライアントとサーバー間の JS 相互運用操作は、サーバーのログにインスタンスと共に記録され <xref:Microsoft.Extensions.Logging.ILogger> ます。 Blazorは、実際のイベントや JS 相互運用機能の入力や出力など、機密情報のログ記録を回避します。

サーバーでエラーが発生すると、フレームワークによってクライアントに通知され、セッションが破棄されます。 既定では、クライアントは、ブラウザーの開発者ツールに表示される一般的なエラーメッセージを受け取ります。

クライアント側のエラーには、呼び出し履歴は含まれず、エラーの原因についての詳細は記載されていませんが、サーバーログにはこのような情報が含まれています。 開発目的では、詳細なエラーを有効にすることによって、重要なエラー情報をクライアントで使用できるようにすることができます。

JavaScript で詳細なエラーを有効にする方法:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* `DetailedErrors`に設定された構成キー `true` 。アプリ設定ファイル (*appsettings*) で設定できます。 このキーは、の値を持つ環境変数を使用して設定することもでき `ASPNETCORE_DETAILEDERRORS` `true` ます。

> [!WARNING]
> インターネット上のクライアントにエラー情報を公開することは、常に避ける必要があるセキュリティ上のリスクです。

### <a name="protect-information-in-transit-with-https"></a>HTTPS を使用した転送中の情報の保護

Blazorサーバーは、 SignalR クライアントとサーバー間の通信にを使用します。 Blazor通常、サーバーはネゴシエートするトランスポートを使用し SignalR ます。これは通常、websocket です。

Blazorサーバーは、サーバーとクライアントの間で送信されるデータの整合性と機密性を保証しません。 常に HTTPS を使用します。

### <a name="cross-site-scripting-xss"></a>クロスサイトスクリプティング (XSS)

クロスサイトスクリプティング (XSS) を使用すると、承認されていないパーティは、ブラウザーのコンテキストで任意のロジックを実行できます。 侵害されたアプリは、クライアントで任意のコードを実行する可能性があります。 脆弱性により、サーバーに対していくつかの悪意のある操作が実行される可能性があります。

* 偽/無効なイベントをサーバーにディスパッチします。
* ディスパッチの失敗/無効なレンダリング入力候補です。
* レンダリング入力候補のディスパッチを回避します。
* JavaScript から .NET への相互運用呼び出しをディスパッチします。
* .NET から JavaScript への相互運用呼び出しの応答を変更します。
* .NET から JS への相互運用の結果をディスパッチしないでください。

Blazorサーバーフレームワークは、前述のいくつかの脅威から保護するための手順を実行します。

* クライアントがレンダーバッチを受信確認していない場合、新しい UI 更新の生成を停止します。 で構成され <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> ます。
* クライアントからの応答を受信せずに、1分後に .NET から JavaScript への呼び出しをタイムアウトにします。 で構成され <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> ます。
* JS 相互運用中にブラウザーからのすべての入力に対して基本的な検証を実行します。
  * .Net 参照は、.NET メソッドによって予期される型と同じです。
  * データの形式が正しくありません。
  * ペイロードには、メソッドの正しい引数数が含まれています。
  * メソッドを呼び出す前に、引数または結果を正しく逆シリアル化できます。
* 次のように、ブラウザーからのすべての入力で、ディスパッチされたイベントから基本的な検証を実行します。
  * イベントに有効な型があります。
  * イベントのデータを逆シリアル化できます。
  * イベントに関連付けられているイベントハンドラーがあります。

フレームワークが実装するセーフガードに加えて、アプリは、脅威から保護し、適切なアクションを実行するために、開発者によってコーディングされている必要があります。

* イベントを処理するときは常にデータを検証します。
* 無効なデータの受信時に適切な操作を行います。
  * データを無視してを返します。 これにより、アプリは要求の処理を続行できます。
  * アプリが、入力が違法であり、正当なクライアントによって生成できなかったと判断した場合は、例外をスローします。 例外をスローすると、回線が破棄され、セッションが終了します。
* ログに含まれるレンダーバッチの完了によって提供されるエラーメッセージを信頼しません。 このエラーはクライアントによって提供され、通常は信頼できません。クライアントが危害を受ける可能性があるためです。
* JavaScript と .NET メソッドの間では、どちらの方向でも、JS 相互運用機能呼び出しの入力を信頼しないでください。
* 引数または結果が正しく逆シリアル化された場合でも、引数と結果の内容が有効であることを検証するのはアプリの役割です。

XSS の脆弱性が存在するようにするには、レンダリングされたページにユーザー入力を組み込む必要があります。 Blazorサーバーコンポーネントは、 *razor*ファイル内のマークアップが手続き型の C# ロジックに変換されるコンパイル時の手順を実行します。 実行時には、C# ロジックによって、要素、テキスト、および子コンポーネントを記述する*レンダリングツリー*が構築されます。 これは、JavaScript 命令のシーケンスを使用してブラウザーの DOM に適用されます (または、プリレンダリングの場合は HTML にシリアル化されます)。

* 通常の構文 (たとえば、) を使用してレンダリングされるユーザー入力では、 Razor `@someStringValue` Razor テキストのみを書き込むことができるコマンドを使用して DOM に構文を追加するため、XSS 脆弱性は公開されません。 値に HTML マークアップが含まれている場合でも、値は静的なテキストとして表示されます。 プリレンダリングすると、出力は HTML エンコードされ、コンテンツも静的テキストとして表示されます。
* スクリプトタグは許可されていないため、アプリのコンポーネントレンダリングツリーに含めることはできません。 コンポーネントのマークアップにスクリプトタグが含まれている場合は、コンパイル時エラーが生成されます。
* コンポーネントの作成者は、を使用せずに、C# でコンポーネントを作成でき Razor ます。 コンポーネントの作成者は、出力の生成時に適切な Api を使用します。 たとえば、とは使用 `builder.AddContent(0, someUserSuppliedString)` し*ませ*ん `builder.AddMarkupContent(0, someUserSuppliedString)` 。後者は XSS 脆弱性を作成する可能性があります。

XSS 攻撃からの保護の一環として、[コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)などの xss 軽減策を実装することを検討してください。

詳細については、「<xref:security/cross-site-scripting>」を参照してください。

### <a name="cross-origin-protection"></a>クロスオリジン保護

クロスオリジン攻撃では、サーバーに対してアクションを実行する別のオリジンのクライアントが必要になります。 悪意のあるアクションは通常、GET 要求またはフォームポスト (クロスサイト要求偽造、CSRF) ですが、悪意のある WebSocket を開くことも可能です。 Blazorサーバーアプリでは、 [ SignalR ハブプロトコルを使用している他のすべてのアプリでも同じ保証が](xref:signalr/security)提供されます。

* Blazorサーバーアプリは、追加の手段を講じて防ぐことができない限り、クロスオリジンにアクセスできます。 クロスオリジンアクセスを無効にするには、エンドポイントで cors を無効にします。そのためには、パイプラインに CORS ミドルウェアを追加し、を <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> Blazor エンドポイントメタデータに追加するか、 [ SignalR クロスオリジンリソース共有を構成](xref:signalr/security#cross-origin-resource-sharing)して、許可されるオリジンのセットを制限します。
* CORS が有効になっている場合、CORS の構成によっては、アプリを保護するために追加の手順が必要になることがあります。 CORS がグローバルに有効になっている場合、 Blazor <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> エンドポイントルートビルダーでを呼び出した後にメタデータをエンドポイントメタデータに追加することによって、サーバーハブに対して cors を無効にすることができます。

詳細については、「<xref:security/anti-request-forgery>」を参照してください。

### <a name="click-jacking"></a>[-Jacking] をクリックします。

[-Jacking] をクリックすると、 `<iframe>` ユーザーが攻撃を受けたサイトでの操作を実行するために、別の配信元のサイトの内部にサイトが表示されます。

内でのレンダリングからアプリを保護するには `<iframe>` 、[コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)とヘッダーを使用し `X-Frame-Options` ます。 詳細については、 [MDN web ドキュメント: X フレームオプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)に関するドキュメントを参照してください。

### <a name="open-redirects"></a>リダイレクトを開く

Blazorサーバーアプリセッションが開始されると、サーバーは、セッションの開始時に送信される url の基本的な検証を実行します。 フレームワークは、回線を確立する前に、ベース URL が現在の URL の親であることを確認します。 フレームワークでは、追加のチェックは実行されません。

ユーザーがクライアントでリンクを選択すると、リンクの URL がサーバーに送信されます。これにより、実行するアクションが決まります。 たとえば、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることができます。

コンポーネントは、を使用してプログラムでナビゲーション要求をトリガーすることもでき <xref:Microsoft.AspNetCore.Components.NavigationManager> ます。 このようなシナリオでは、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりすることがあります。

コンポーネントが必要:

* ナビゲーション呼び出しの引数の一部としてユーザー入力を使用することは避けてください。
* 引数を検証して、ターゲットがアプリで許可されていることを確認します。

そうしないと、悪意のあるユーザーが、攻撃者によって制御されたサイトに強制的にアクセスすることができます。 このシナリオでは、攻撃者は、メソッドの呼び出しの一部として、ユーザー入力を使用してアプリに対してトリックを <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 行います。

このアドバイスは、アプリの一部としてリンクを表示する場合にも適用されます。

* 可能であれば、相対リンクを使用します。
* ページに含める前に、絶対リンク先が有効であることを検証します。

詳細については、「<xref:security/preventing-open-redirects>」を参照してください。

## <a name="security-checklist"></a>セキュリティ チェックリスト

次のセキュリティの考慮事項の一覧は、包括的なものではありません。

* イベントから引数を検証します。
* JS 相互運用機能呼び出しからの入力と結果を検証します。
* .NET から JS への相互運用呼び出しに対して (または事前に検証する) ユーザー入力を使用しないようにします。
* クライアントが、バインドされていないメモリの量を割り当てないようにします。
  * コンポーネント内のデータ。
  * `DotNetObject`クライアントに返される参照。
* 複数のディスパッチに対して保護します。
* コンポーネントが破棄されるときに、実行時間の長い操作をキャンセルします。
* 大量のデータを生成するイベントを回避します。
* の呼び出しの一部としてユーザー入力を使用せず <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 、許可されたオリジンのセットに対する url のユーザー入力を、回避できない場合は先に検証します。
* UI の状態に基づいて承認を決定するのではなく、コンポーネントの状態のみを確認してください。
* [コンテンツセキュリティポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)を使用して XSS 攻撃から保護することを検討してください。
* クリック時の確認を防止するには、CSP と[X フレームオプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)を使用することを検討してください。
* Cors を有効にする場合や、アプリの CORS を明示的に無効にする場合は、CORS 設定が適切であることを確認し Blazor ます
* アプリケーションのサーバー側の制限が許容できるレベルの Blazor リスクなしで許容できるユーザーエクスペリエンスを提供することをテストします。
