---
title: ASP.NET Core Blazor 状態管理
author: guardrex
description: Blazor サーバー側アプリで状態を永続化する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/state-management
ms.openlocfilehash: af040635302fbf2dae8192dcf37d55bfcfedfcec
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030371"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core Blazor 状態管理

作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor は、ステートフルなアプリケーションフレームワークです。 ほとんどの場合、アプリはサーバーへの継続的な接続を維持します。 ユーザーの状態は、*回線*内のサーバーのメモリに保持されます。 

ユーザーの回線に保持されている状態の例を次に示します。

* コンポーネントインスタンスの&mdash;階層と最新のレンダリング出力の UI。
* コンポーネントインスタンス内のフィールドとプロパティの値。
* [依存関係の注入 (DI)](xref:fundamentals/dependency-injection)サービスインスタンスに保持されているデータ (回線にスコープされる)。

> [!NOTE]
> この記事では、Blazor サーバー側アプリの状態の永続化について説明します。 Blazor クライアント側アプリは、[ブラウザーでのクライアント側の状態の永続](#client-side-in-the-browser)化を利用できますが、この記事の範囲を超えてカスタムソリューションまたはサードパーティのパッケージが必要です。

## <a name="blazor-circuits"></a>Blazor 回線

ユーザーが一時的なネットワーク接続の損失を発生した場合、Blazor はユーザーを元の回線に再接続して、アプリを引き続き使用できるようにします。 ただし、ユーザーをサーバーのメモリ内の元の回線に再接続することは、常に可能であるとは限りません。

* サーバーは切断された回線を永続的に保持できません。 サーバーは、タイムアウト後またはサーバーのメモリ不足が発生したときに、切断された回線を解放する必要があります。
* マルチサーバー、負荷分散された展開環境では、任意の時点でサーバー処理要求が使用できなくなることがあります。 要求の全体的な量を処理する必要がなくなった場合は、個々のサーバーに障害が発生するか、自動的に削除される可能性があります。 ユーザーが再接続しようとすると、元のサーバーが使用できなくなる可能性があります。
* ユーザーはブラウザーを閉じて再度開くか、ページを再度読み込んで、ブラウザーのメモリに保持されている状態をすべて削除することができます。 たとえば、JavaScript の相互運用呼び出しによって設定された値は失われます。

ユーザーが元の回線に再接続できない場合、ユーザーは新しい回線を空の状態で受信します。 これは、デスクトップアプリを閉じて再度開くことと同じです。

## <a name="preserve-state-across-circuits"></a>回線間で状態を保持する

場合によっては、回線間で状態を維持することをお勧めします。 次の場合、アプリはユーザーの重要なデータを保持できます。

* Web サーバーが使用できなくなります。
* ユーザーのブラウザーは、新しい web サーバーで新しい回線を開始することを強制されます。

一般に、回線をまたいで状態を維持することは、既に存在するデータを単に読み取るだけではなく、ユーザーが積極的にデータを作成するシナリオに適用されます。

1つの回線を超えて状態を維持するために、*データをサーバーのメモリに格納するだけではいけません*。 アプリは、他の保存場所にデータを保持する必要があります。 状態の永続化&mdash;は自動ではありません。アプリケーションを開発してステートフルなデータ永続化を実装するには、手順を実行する必要があります

通常、データの永続化は、ユーザーが作成に労力を費やす高価値状態の場合にのみ必要です。 次の例では、状態の永続化によって時間が節約されるか、商用活動の支援が行われます。

* 複数の&ndash;手順を追った web フォームでは、ユーザーが状態が失われた場合に、複数の手順で完了したプロセスのデータを再入力するのに時間がかかります。 このシナリオでは、ユーザーがマルチステップ形式から移動し、後でフォームに戻ると、状態が失われます。
* ショッピングカート&ndash;は、潜在的な収益を表すアプリの市販の重要なコンポーネントを維持できます。 自分の状態やショッピングカートを紛失したユーザーは、後でサイトに戻ったときに、より多くの製品やサービスを購入することができます。

通常、送信されていないサインインダイアログに入力したユーザー名など、簡単に再作成された状態を維持する必要はありません。

> [!IMPORTANT]
> アプリは、アプリの*状態*のみを保持できます。 コンポーネントインスタンスやそのレンダリングツリーなど、Ui を永続化することはできません。 コンポーネントとレンダリングツリーは、一般にシリアル化できません。 TreeView の展開されたノードなど、UI の状態に似たものを保持するには、アプリにカスタムコードを用意して、動作を serializable アプリの状態としてモデル化する必要があります。

## <a name="where-to-persist-state"></a>状態を保持する場所

Blazor サーバー側アプリで状態を永続化するための一般的な場所は3つあります。 各アプローチは、さまざまなシナリオに最適であり、さまざまな注意点があります。

* [データベース内のサーバー側](#server-side-in-a-database)
* [URL](#url)
* [クライアント側 (ブラウザー)](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>データベース内のサーバー側

永続的なデータ永続化や、複数のユーザーまたはデバイスにまたがる必要があるデータについては、独立したサーバー側データベースが最適な選択肢です。 次のオプションがあります。

* リレーショナル SQL データベース
* キー/値ストア
* Blob ストア
* テーブルストア

データがデータベースに保存された後は、ユーザーがいつでも新しい回線を開始できます。 ユーザーのデータは、すべての新しい回線で保持され、使用できます。

Azure のデータストレージオプションの詳細については、 [Azure Storage のドキュメント](/azure/storage/)と[azure データベース](https://azure.microsoft.com/product-categories/databases/)を参照してください。

### <a name="url"></a>URL

ナビゲーション状態を表す一時的なデータの場合は、URL の一部としてデータをモデル化します。 URL でモデル化された状態の例を次に示します。

* 表示されているエンティティの ID。
* ページングされたグリッド内の現在のページ番号。

ブラウザーのアドレスバーの内容は保持されます。

* ユーザーが手動でページを再読み込みした場合。
* Web サーバーが使用できなく&mdash;なった場合、ユーザーは別のサーバーに接続するために、ページの再読み込みを強制されます。

`@page`ディレクティブを使用して URL パターンを定義する方法<xref:blazor/routing>の詳細については、「」を参照してください。

### <a name="client-side-in-the-browser"></a>クライアント側 (ブラウザー)

ユーザーがアクティブに作成している一時的なデータの場合、共通のバッキングストア`localStorage`は`sessionStorage` 、ブラウザーのおよびコレクションです。 回線が破棄された場合、保存されている状態を管理またはクリアするためにアプリは必要ありません。これは、サーバー側の記憶域よりも利点があります。

> [!NOTE]
> このセクションの「クライアント側」では、 [Blazor クライアント側のホスティングモデル](xref:blazor/hosting-models#client-side)ではなく、ブラウザーでのクライアント側のシナリオを参照しています。 `localStorage`と`sessionStorage`は、Blazor クライアント側アプリで使用できますが、カスタムコードを記述したり、サードパーティのパッケージを使用したりすることによってのみ使用できます。

`localStorage`と`sessionStorage`は次のように異なります。

* `localStorage`は、ユーザーのブラウザーにスコープが設定されています。 ユーザーがページを再読み込みするか、ブラウザーを閉じてから再度開くと、状態は維持されます。 ユーザーが複数のブラウザータブを開いた場合、状態はタブ間で共有されます。 明示的に`localStorage`クリアされるまで、データはに保持されます。
* `sessionStorage`は、ユーザーのブラウザータブにスコープが設定されています。ユーザーがタブを再読み込みすると、状態は維持されます。 ユーザーがタブまたはブラウザーを閉じた場合、状態は失われます。 ユーザーが複数のブラウザータブを開いた場合、各タブには独立したバージョンのデータが含まれます。

一般に`sessionStorage` 、を使用する方が安全です。 `sessionStorage`ユーザーが複数のタブを開いて、次のことが発生するリスクを回避します。

* タブ間の状態ストレージのバグ。
* タブが他のタブの状態を上書きするときの動作がわかりにくい。

`localStorage`は、ブラウザーを閉じて再度開いている間にアプリが状態を保持する必要がある場合に適しています。

ブラウザーの記憶域を使用する場合の注意事項:

* サーバー側データベースを使用する場合と同様に、データの読み込みと保存は非同期です。
* サーバー側データベースとは異なり、プリステージ中に要求されたページがブラウザーに存在しないため、プリステージ中にストレージを使用することはできません。
* Blazor のサーバー側アプリでは、数キロバイトのデータを保存するのが妥当です。 ネットワーク経由でデータが読み込まれ、保存されるため、数キロバイトを超えるパフォーマンスへの影響を考慮する必要があります。
* ユーザーは、データの表示や改ざんを行うことができます。 ASP.NET Core[データ保護](xref:security/data-protection/introduction)を使用すると、リスクを軽減できます。

## <a name="third-party-browser-storage-solutions"></a>サードパーティのブラウザーストレージソリューション

サードパーティの NuGet パッケージは`localStorage` 、および`sessionStorage`を操作するための api を提供します。

ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択することを検討してください。 ASP.NET Core データ保護は、格納されているデータを暗号化し、格納されたデータを改ざんするリスクを軽減します。 JSON でシリアル化されたデータがプレーンテキストで格納されている場合、ユーザーはブラウザー開発者ツールを使用してデータを表示し、格納されているデータも変更できます。 データのセキュリティ保護は、本質的には単純であるため、常に問題になるとは限りません。 たとえば、UI 要素に格納されている色の読み取りや変更は、ユーザーや組織にとって重要なセキュリティ上のリスクとは言えません。 *機密データ*を検査または改ざんすることをユーザーに許可しないでください。

## <a name="protected-browser-storage-experimental-package"></a>保護されたブラウザーストレージの試験的パッケージ

`localStorage` と `sessionStorage` の[データ保護](xref:security/data-protection/introduction)を提供するNuGetパッケージの例は、[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)です。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`現時点では、サポートされていない実験用パッケージが運用環境で使用するのに適していません。

### <a name="installation"></a>インストール

`Microsoft.AspNetCore.ProtectedBrowserStorage`パッケージをインストールするには:

1. Blazor サーバー側アプリプロジェクトで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)へのパッケージ参照を追加します。
1. 最上位レベルの HTML (たとえば、既定のプロジェクトテンプレートの*Pages/_Host*ファイル) で、次`<script>`のタグを追加します。

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. メソッドで、を呼び出し`AddProtectedBrowserStorage`てサービス`localStorage`コレクション`sessionStorage`におよびサービスを追加します。 `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>コンポーネント内のデータを保存して読み込む

ブラウザーストレージへのデータの読み込みまたは保存を必要とする[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)コンポーネントでは、を使用して、次のいずれかのインスタンスを挿入します。

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選択は、使用するバッキングストアによって異なります。 次の例では`sessionStorage` 、が使用されています。

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

ステートメント`@using`は、コンポーネントではなく、*インポートの razor*ファイルに配置できます。 *インポートの razor*ファイルを使用すると、アプリまたはアプリ全体で名前空間を使用できるようになります。

プロジェクトテンプレートの`currentCount` `Counter`コンポーネントに値を保持するには、 `ProtectedSessionStore.SetAsync`次のようにメソッドを変更します。`IncrementCount`

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

大規模でより現実的なアプリでは、個々のフィールドを格納するシナリオはほとんどありません。 アプリは、複雑な状態を含むモデルオブジェクト全体を格納する可能性が高くなります。 `ProtectedSessionStore`JSON データを自動的にシリアル化および逆シリアル化します。

前のコード例`currentCount`では、データはとして`sessionStorage['count']`ユーザーのブラウザーに格納されます。 データはプレーンテキストで保存されるのではなく、ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を使用して保護されます。 暗号化されたデータは、 `sessionStorage['count']`ブラウザーの開発者コンソールでが評価された場合に表示されます。

ユーザーが後`currentCount`で (まったく新しい回線を使用`Counter` `ProtectedSessionStore.GetAsync`しているかどうかを含めて) コンポーネントに戻ったときにデータを回復するには、次のように指定します。

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

コンポーネントのパラメーターにナビゲーション状態が含まれて`ProtectedSessionStore.GetAsync`いる場合は、を`OnParametersSetAsync`呼び出し、 `OnInitializedAsync`ではなく結果をに代入します。 `OnInitializedAsync`は、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。 `OnInitializedAsync`は、ユーザーが同じページで別の URL に移動した場合に、後で再度呼び出されることはありません。

> [!WARNING]
> このセクションの例は、サーバーのプリレンダリングが有効になっていない場合にのみ機能します。 プリレンダリングが有効になっていると、次のようなエラーが生成されます。
>
> > この時点では、JavaScript の相互運用呼び出しは発行できません。 これは、コンポーネントが prerendered されているためです。
>
> プリレンダリングを無効にするか、またはプリコーディングを使用するコードを追加します。 プリレンダリングで動作するコードの記述の詳細については、「[処理のプリ](#handle-prerendering)コーディング」を参照してください。

### <a name="handle-the-loading-state"></a>読み込み状態を処理する

ブラウザーストレージは非同期 (ネットワーク接続経由でアクセスされる) であるため、データが読み込まれてコンポーネントで使用できるようになるまでには、常に一定の時間があります。 最適な結果を得るには、読み込み中に、空白または既定のデータを表示するのではなく、読み込み状態メッセージを表示します。

1つの方法は、データが`null` (まだ読み込み中である) かどうかを追跡することです。 既定`Counter`のコンポーネントでは、カウントは`int`に保持されます。 型`currentCount` `?`()`int`に疑問符 () を追加して null 値を許容するようにします。

```csharp
private int? currentCount;
```

[カウントと**インクリメント**] ボタンを無条件に表示するのではなく、データが読み込まれた場合にのみこれらの要素を表示するように選択します。

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>プリレンダリングの処理

プリレンダリング中:

* ユーザーのブラウザーへの対話型接続は存在しません。
* ブラウザーには、JavaScript コードを実行できるページがまだありません。

`localStorage`また`sessionStorage`はが、プリレンダリング中に使用できません。 コンポーネントがストレージを操作しようとすると、次のようなエラーが生成されます。

> この時点では、JavaScript の相互運用呼び出しは発行できません。 これは、コンポーネントが prerendered されているためです。

このエラーを解決する方法の1つは、プリレンダリングを無効にすることです。 これは通常、アプリでブラウザーベースのストレージを多用する場合に最適な選択肢です。 プリレンダリングによって複雑さが`localStorage`増し、アプリには`sessionStorage`メリットがありません

プリレンダリングを無効にするには

1. *Pages/_Host*ファイルを開き、へ`Html.RenderComponentAsync`の呼び出しを削除します。
1. ファイルを開き、の`endpoints.MapBlazorHub()`呼び出し`endpoints.MapBlazorHub<App>("app")`をに置き換えます。 `Startup.cs` `App`ルートコンポーネントの型を示します。 `"app"`は、ルートコンポーネントの場所を指定する CSS セレクターです。

またはを使用`localStorage`しない他のページでは`sessionStorage`、プリレンダリングが役立つ場合があります。 プリレンダリングが有効な状態を維持するには、ブラウザーが回線に接続されるまで読み込み操作を延期します。 次に、カウンター値を格納する例を示します。

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync()
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>共通の場所に保持される状態を考慮する

多くのコンポーネントがブラウザーベースのストレージに依存している場合は、状態プロバイダーコードを何度も再実装すると、コードの重複が発生します。 コードの重複を回避するためのオプションの1つは、状態プロバイダーのロジックをカプセル化する*状態プロバイダーの親コンポーネント*を作成することです。 子コンポーネントは、状態の永続化メカニズムに関係なく、永続化されたデータを処理できます。

次の`CounterStateProvider`コンポーネントの例では、カウンターデータが永続化されます。

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

読み込み`CounterStateProvider`が完了するまで子コンテンツをレンダリングしないことで、コンポーネントは読み込みフェーズを処理します。

`CounterStateProvider`コンポーネントを使用するには、カウンターの状態へのアクセスを必要とする他のコンポーネントの周囲にコンポーネントのインスタンスをラップします。 アプリ内の`CounterStateProvider`すべての`Router` `App`コンポーネントから状態にアクセスできるようにするには、コンポーネント (*app.xaml*) のを周囲にコンポーネントをラップします。

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

ラップされたコンポーネントはを受け取り、永続化されたカウンターの状態を変更できます。 次`Counter`のコンポーネントは、というパターンを実装しています。

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

前のコンポーネントは、との`ProtectedBrowserStorage`対話には必要ありません。また、"読み込み中" フェーズも処理しません。

前に説明したように、 `CounterStateProvider`プリレンダリングを処理するには、カウンターデータを使用するすべてのコンポーネントがプリレンダリングで自動的に動作するように、を修正できます。 詳細については、「[ハンドルのプリレンダリング](#handle-prerendering)」を参照してください。

一般に、*状態プロバイダーの親コンポーネント*パターンをお勧めします。

* 他の多くのコンポーネントで状態を使用する場合。
* 保持する最上位レベルの状態オブジェクトが1つだけの場合。

さまざまな状態オブジェクトを保持し、異なる場所にあるオブジェクトの異なるサブセットを使用するには、状態の読み込みと保存をグローバルに処理しないことをお勧めします。