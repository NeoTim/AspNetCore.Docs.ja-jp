---
title: ASP.NET Core Blazor の高度なシナリオ
author: guardrex
description: Blazor の高度なシナリオについて説明します。これには、手動の RenderTreeBuilder ロジックをアプリに組み込む方法などが含まれます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 3345f545e230ada78e6c66fc9eb049060d5794d6
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851160"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="d7c88-103">ASP.NET Core Blazor の高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="d7c88-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="d7c88-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d7c88-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor<span data-ttu-id="d7c88-105"> サーバー回線ハンドラー</span><span class="sxs-lookup"><span data-stu-id="d7c88-105"> Server circuit handler</span></span>

Blazor<span data-ttu-id="d7c88-106"> サーバーを使用すると、コードで "*回線ハンドラー*" を定義できます。これにより、ユーザーの回線の状態の変更時にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-106"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="d7c88-107">回線ハンドラーは、`CircuitHandler` から派生させ、そのクラスをアプリのサービス コンテナーに登録することで実装します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="d7c88-108">次の回線ハンドラーの例では、開いている SignalR 接続を追跡します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="d7c88-109">回線ハンドラーは DI を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="d7c88-110">スコープを持つインスタンスは、回線のインスタンスごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="d7c88-111">前の例の `TrackingCircuitHandler` を使用すると、すべての回線の状態を追跡する必要があるため、シングルトン サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="d7c88-112">カスタム回線ハンドラーのメソッドでハンドルされない例外がスローされる場合は、その例外は Blazor サーバー回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="d7c88-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="d7c88-113">ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログを含む 1 つ以上の [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでコードをラップします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="d7c88-114">ユーザーが切断し、フレームワークで回線の状態がクリーンアップされていることが原因で回線が終了すると、フレームワークによって回線の DI スコープが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="d7c88-115">スコープが破棄されると、<xref:System.IDisposable?displayProperty=fullName> を実装するサーキットスコープの DI サービスはすべて破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="d7c88-116">破棄中にいずれかの DI サービスでハンドルされない例外がスローされると、フレームワークによって例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="d7c88-117">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="d7c88-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="d7c88-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> には、コンポーネントと要素を操作するためのメソッドが用意されています。これには、C# コードでコンポーネントを手動で作成することも含まれます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="d7c88-119">コンポーネントの作成に <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> を使用することは、高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="d7c88-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="d7c88-120">形式が正しくないコンポーネント (閉じられていないマークアップ タグなど) により、定義されていない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="d7c88-121">次の `PetDetails` コンポーネントを考えてみましょう。これは手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="d7c88-122">次の例では、`CreateComponent` メソッド内のループによって、3 つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="d7c88-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソース コードの行番号になります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-123">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="d7c88-124">Blazor の差分アルゴリズムは、個別の呼び出しではなく、個別のコード行に対応するシーケンス番号に依存しています。</span><span class="sxs-lookup"><span data-stu-id="d7c88-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="d7c88-125"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコードします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="d7c88-126">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="d7c88-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="d7c88-127">詳細については、「[シーケンス番号は実行順序ではなくコード行番号に関係する](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7c88-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="d7c88-128">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="d7c88-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="d7c88-129"><xref:Microsoft.AspNetCore.Components.RenderTree> の型により、レンダリング操作の "*結果*" の処理が許可されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="d7c88-130">これらは、Blazor フレームワーク実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="d7c88-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="d7c88-131">これらの型は "*不安定*" と考えるべきで、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="d7c88-132">シーケンス番号は実行順序ではなくコード行番号に関係する</span><span class="sxs-lookup"><span data-stu-id="d7c88-132">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="d7c88-133"> コンポーネント ファイル ( *.razor*) は常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-133"> component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="d7c88-134">コンパイル ステップは、実行時にアプリのパフォーマンスを向上させる情報を挿入するために使用できるため、コンパイルの方がコードを解釈するよりも潜在的な利点があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="d7c88-135">これらの機能強化の主な例として、"*シーケンス番号*" があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="d7c88-136">シーケンス番号は、出力がコードの個別の順序付けられたどの行からのものかをランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="d7c88-137">ランタイムでは、この情報を使用して、効率的なツリーの差分を線形時間で生成します。これは、一般的なツリーの差分アルゴリズムで通常にできるよりもかなり高速です。</span><span class="sxs-lookup"><span data-stu-id="d7c88-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="d7c88-138">次の Razor コンポーネント ( *.razor*) ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d7c88-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="d7c88-139">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="d7c88-140">コードを初めて実行するときに、`someFlag` が `true` の場合、ビルダーは以下を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="d7c88-141">シーケンス</span><span class="sxs-lookup"><span data-stu-id="d7c88-141">Sequence</span></span> | <span data-ttu-id="d7c88-142">種類</span><span class="sxs-lookup"><span data-stu-id="d7c88-142">Type</span></span>      | <span data-ttu-id="d7c88-143">データ</span><span class="sxs-lookup"><span data-stu-id="d7c88-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d7c88-144">0</span><span class="sxs-lookup"><span data-stu-id="d7c88-144">0</span></span>        | <span data-ttu-id="d7c88-145">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-145">Text node</span></span> | <span data-ttu-id="d7c88-146">First</span><span class="sxs-lookup"><span data-stu-id="d7c88-146">First</span></span>  |
| <span data-ttu-id="d7c88-147">1</span><span class="sxs-lookup"><span data-stu-id="d7c88-147">1</span></span>        | <span data-ttu-id="d7c88-148">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-148">Text node</span></span> | <span data-ttu-id="d7c88-149">Second</span><span class="sxs-lookup"><span data-stu-id="d7c88-149">Second</span></span> |

<span data-ttu-id="d7c88-150">`someFlag` が `false` になり、マークアップが再びレンダリングされるとします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="d7c88-151">今度は、ビルダーは以下を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-151">This time, the builder receives:</span></span>

| <span data-ttu-id="d7c88-152">シーケンス</span><span class="sxs-lookup"><span data-stu-id="d7c88-152">Sequence</span></span> | <span data-ttu-id="d7c88-153">種類</span><span class="sxs-lookup"><span data-stu-id="d7c88-153">Type</span></span>       | <span data-ttu-id="d7c88-154">データ</span><span class="sxs-lookup"><span data-stu-id="d7c88-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="d7c88-155">1</span><span class="sxs-lookup"><span data-stu-id="d7c88-155">1</span></span>        | <span data-ttu-id="d7c88-156">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-156">Text node</span></span>  | <span data-ttu-id="d7c88-157">Second</span><span class="sxs-lookup"><span data-stu-id="d7c88-157">Second</span></span> |

<span data-ttu-id="d7c88-158">ランタイムで差分を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な "*編集スクリプト*" が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="d7c88-159">Remove the first text node. (最初のテキスト ノードを削除します。)</span><span class="sxs-lookup"><span data-stu-id="d7c88-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="d7c88-160">プログラムによってシーケンス番号を生成する場合の問題</span><span class="sxs-lookup"><span data-stu-id="d7c88-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="d7c88-161">代わりに、次のレンダリング ツリー ビルダー ロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="d7c88-162">最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-162">Now, the first output is:</span></span>

| <span data-ttu-id="d7c88-163">シーケンス</span><span class="sxs-lookup"><span data-stu-id="d7c88-163">Sequence</span></span> | <span data-ttu-id="d7c88-164">種類</span><span class="sxs-lookup"><span data-stu-id="d7c88-164">Type</span></span>      | <span data-ttu-id="d7c88-165">データ</span><span class="sxs-lookup"><span data-stu-id="d7c88-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="d7c88-166">0</span><span class="sxs-lookup"><span data-stu-id="d7c88-166">0</span></span>        | <span data-ttu-id="d7c88-167">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-167">Text node</span></span> | <span data-ttu-id="d7c88-168">First</span><span class="sxs-lookup"><span data-stu-id="d7c88-168">First</span></span>  |
| <span data-ttu-id="d7c88-169">1</span><span class="sxs-lookup"><span data-stu-id="d7c88-169">1</span></span>        | <span data-ttu-id="d7c88-170">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-170">Text node</span></span> | <span data-ttu-id="d7c88-171">Second</span><span class="sxs-lookup"><span data-stu-id="d7c88-171">Second</span></span> |

<span data-ttu-id="d7c88-172">この結果は前のケースと同じであるため、否定的な問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="d7c88-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="d7c88-173">`someFlag` は 2 番目のレンダリングでは `false` で、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="d7c88-174">シーケンス</span><span class="sxs-lookup"><span data-stu-id="d7c88-174">Sequence</span></span> | <span data-ttu-id="d7c88-175">種類</span><span class="sxs-lookup"><span data-stu-id="d7c88-175">Type</span></span>      | <span data-ttu-id="d7c88-176">データ</span><span class="sxs-lookup"><span data-stu-id="d7c88-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="d7c88-177">0</span><span class="sxs-lookup"><span data-stu-id="d7c88-177">0</span></span>        | <span data-ttu-id="d7c88-178">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="d7c88-178">Text node</span></span> | <span data-ttu-id="d7c88-179">Second</span><span class="sxs-lookup"><span data-stu-id="d7c88-179">Second</span></span> |

<span data-ttu-id="d7c88-180">今度は、差分アルゴリズムによって、"*2 つ*" の変更が発生したことが認識され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="d7c88-181">最初のテキスト ノードの値を `Second` に変更します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="d7c88-182">2 番目のテキスト ノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-182">Remove the second text node.</span></span>

<span data-ttu-id="d7c88-183">シーケンス番号を生成すると、`if/else` 分岐とループが元のコードのどこにあったかに関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="d7c88-184">これにより、差分が以前の **2 倍の長さ**なります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="d7c88-185">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="d7c88-185">This is a trivial example.</span></span> <span data-ttu-id="d7c88-186">複雑で深く入れ子になった構造体で、特にループがある、より現実的なケースでは、通常、パフォーマンス コストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="d7c88-187">どのループ ブロックまたは分岐が挿入または削除されたかを即座に特定する代わりに、差分アルゴリズムではレンダリング ツリー内を深く再帰処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="d7c88-188">これにより、古い構造と新しい構造体が相互にどのように関連しているかについて差分アルゴリズムが誤って通知されるため、通常、より長い編集スクリプトを作成することが必要になります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="d7c88-189">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="d7c88-189">Guidance and conclusions</span></span>

* <span data-ttu-id="d7c88-190">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="d7c88-191">コンパイル時にキャプチャされない限り、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="d7c88-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="d7c88-192">手動で実装された <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="d7c88-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="d7c88-193">" *.razor*" ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="d7c88-194">手動の <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> ロジックを回避できない場合は、長いブロックのコードを <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 呼び出しでラップされたより小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="d7c88-195">各リージョンには独自のシーケンス番号の個別のスペースがあるため、各リージョン内でゼロ (または他の任意の数) から再開できます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="d7c88-196">シーケンス番号がハードコードされている場合、差分アルゴリズムでは、シーケンス番号の値が増えることだけが要求されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="d7c88-197">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="d7c88-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="d7c88-198">合理的な選択肢の 1 つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1 つずつまたは 100 ずつ (または任意の間隔で) 増やすことです。</span><span class="sxs-lookup"><span data-stu-id="d7c88-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="d7c88-199"> ではシーケンス番号が使用されていますが、他のツリー差分 UI フレームワークでは使用されていません。</span><span class="sxs-lookup"><span data-stu-id="d7c88-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="d7c88-200">シーケンス番号を使用すると、差分がはるかに高速になります。また、Blazor には、" *.razor*" ファイルを作成する開発者に対して、シーケンス番号を自動的に処理するコンパイル ステップの利点があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="d7c88-201">Blazor サーバー アプリで大規模なデータ転送を実行する</span><span class="sxs-lookup"><span data-stu-id="d7c88-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="d7c88-202">シナリオによっては、JavaScript と Blazor 間で大量のデータを転送する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="d7c88-203">通常、大規模なデータ転送は次の場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="d7c88-204">ブラウザー ファイル システム API が、ファイルをアップロードまたはダウンロードするために使用されている場合。</span><span class="sxs-lookup"><span data-stu-id="d7c88-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="d7c88-205">サードパーティのライブラリとの相互運用が必要な場合。</span><span class="sxs-lookup"><span data-stu-id="d7c88-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="d7c88-206">Blazor サーバーでは、パフォーマンスの問題を引き起こす可能性がある 1 つの大きなメッセージを渡すことができないように制限されています。</span><span class="sxs-lookup"><span data-stu-id="d7c88-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="d7c88-207">JavaScript と Blazor 間でデータを転送するコードを開発するときは、次のガイダンスを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="d7c88-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="d7c88-208">データをより小さな部分にスライスし、すべてのデータがサーバーによって受信されるまでデータ セグメントを順番に送信します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="d7c88-209">JavaScript および C# コードで大きなオブジェクトを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="d7c88-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="d7c88-210">データを送受信するときに、メイン UI スレッドを長時間ブロックしないでください。</span><span class="sxs-lookup"><span data-stu-id="d7c88-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="d7c88-211">プロセスの完了時またはキャンセル時に、消費していたメモリを解放します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="d7c88-212">セキュリティ上の理由から、次の追加要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="d7c88-213">渡すことのできるファイルまたはデータの最大サイズを宣言します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="d7c88-214">クライアントからサーバーへの最小アップロード レートを宣言します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="d7c88-215">データがサーバーによって受信されたら、データは:</span><span class="sxs-lookup"><span data-stu-id="d7c88-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="d7c88-216">すべてのセグメントが収集されるまで、一時的にメモリ バッファーに格納できます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="d7c88-217">直ちに消費できます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-217">Consumed immediately.</span></span> <span data-ttu-id="d7c88-218">たとえば、データは、データベースに直ちに格納することも、セグメントを受信するたびにディスクに書き込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="d7c88-219">次のファイル アップローダー クラスは、クライアントとの JS 相互運用を処理します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="d7c88-220">アップローダー クラスは、JS 相互運用を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="d7c88-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="d7c88-221">データ セグメントを送信するクライアントをポーリングします。</span><span class="sxs-lookup"><span data-stu-id="d7c88-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="d7c88-222">ポーリングがタイムアウトした場合は、トランザクションを中止します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="d7c88-223">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="d7c88-223">In the preceding example:</span></span>

* <span data-ttu-id="d7c88-224">`maxBase64SegmentSize` は `8192` に設定されます。これは `maxBase64SegmentSize = segmentSize * 4 / 3` から計算されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="d7c88-225">低レベルの .NET Core メモリ管理 API は、`uploadedSegments` のサーバーにメモリ セグメントを格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="d7c88-226">`ReceiveFile` メソッドは、JS 相互運用によるアップロードを処理するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="d7c88-227">ファイル サイズは、`jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` を使用した JS 相互運用を通じて、バイト単位で決定されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="d7c88-228">受信するセグメントの数が計算され、`numberOfSegments` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="d7c88-229">セグメントは、`jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` を使用した JS 相互運用を通じて `for` ループで要求されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="d7c88-230">デコードする前のセグメントは、最後を除いてすべてが 8192 バイトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7c88-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="d7c88-231">クライアントは、効率的な方法でデータを送信することを強制されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="d7c88-232">受信した各セグメントに対して、<xref:System.Convert.TryFromBase64String%2A> でデコードする前にチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="d7c88-233">アップロードが完了すると、データを含むストリームが新しい <xref:System.IO.Stream> (`SegmentedStream`) として返されます。</span><span class="sxs-lookup"><span data-stu-id="d7c88-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="d7c88-234">セグメント化されたストリーム クラスは、セグメントの一覧を読み取り専用のシーク可能ではない <xref:System.IO.Stream> として公開します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="d7c88-235">次のコードでは、データを受け取る JavaScript 関数を実装します。</span><span class="sxs-lookup"><span data-stu-id="d7c88-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
