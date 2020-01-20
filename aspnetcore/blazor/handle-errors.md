---
title: ASP.NET Core Blazor アプリでのエラーの処理
author: guardrex
description: Blazor でハンドルされない例外を管理する方法、およびエラーを検出して処理するアプリを開発する方法を ASP.NET Core Blazor 方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: fe4cc13b1efb8c70c9632f032626aa938fb65ea3
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159951"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="b0e7c-103">ASP.NET Core Blazor アプリでのエラーの処理</span><span class="sxs-lookup"><span data-stu-id="b0e7c-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="b0e7c-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="b0e7c-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="b0e7c-105">この記事では、Blazor がハンドルされない例外を管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="b0e7c-106">開発中の詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="b0e7c-106">Detailed errors during development</span></span>

<span data-ttu-id="b0e7c-107">開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="b0e7c-108">エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="b0e7c-109">開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="b0e7c-110">実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="b0e7c-111">このエラー処理エクスペリエンスの UI は、Blazor プロジェクトテンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-111">The UI for this error handling experience is part of the Blazor project templates.</span></span> <span data-ttu-id="b0e7c-112">Blazor WebAssembly で、 *wwwroot/index.html*ファイルのエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="b0e7c-113">Blazor Server アプリで、 *Pages/_Host cshtml*ファイルのエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="b0e7c-114">`blazor-error-ui` 要素は、Blazor テンプレートに含まれるスタイルによって非表示になり、エラーが発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

## <a name="how-the-opno-locblazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="b0e7c-115">Blazor フレームワークがハンドルされない例外にどのように反応するか</span><span class="sxs-lookup"><span data-stu-id="b0e7c-115">How the Blazor framework reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="b0e7c-116"> Server は、ステートフルなフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-116"> Server is a stateful framework.</span></span> <span data-ttu-id="b0e7c-117">ユーザーは、アプリを操作している間、*回線*と呼ばれるサーバーへの接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="b0e7c-118">回線は、アクティブなコンポーネントインスタンスに加えて、次のような状態の他の多くの側面を保持します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="b0e7c-119">コンポーネントの最新のレンダリング出力。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="b0e7c-120">クライアント側のイベントによってトリガーされる可能性がある、現在のイベント処理デリゲートのセット。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="b0e7c-121">ユーザーが複数のブラウザータブでアプリを開いた場合、複数の独立した回路があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="b0e7c-122"> は、ほとんどのハンドルされない例外を、発生した回線にとって致命的なものとして扱います。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="b0e7c-123">ハンドルされない例外によって回線が終了した場合、ユーザーは新しい回線を作成するためにページを再読み込みするだけで、アプリとの対話を続けることができます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="b0e7c-124">他のユーザーまたは他のブラウザータブの回線である、終了した回路以外の回路は影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="b0e7c-125">このシナリオは、クラッシュしたアプリを再起動する必要がある&mdash;クラッシュするデスクトップアプリに似ていますが、他のアプリは影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="b0e7c-126">次の理由により、ハンドルされない例外が発生したときに回線が終了します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="b0e7c-127">ハンドルされない例外が発生すると、回線が未定義の状態のままになることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="b0e7c-128">未処理の例外が発生した後は、アプリの通常の動作を保証できません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="b0e7c-129">回線が継続している場合は、セキュリティの脆弱性がアプリに表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="b0e7c-130">開発者コードでハンドルされない例外を管理する</span><span class="sxs-lookup"><span data-stu-id="b0e7c-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="b0e7c-131">エラー発生後もアプリを続行するには、アプリにエラー処理ロジックが必要です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="b0e7c-132">この記事の後のセクションでは、未処理の例外の潜在的な原因について説明します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="b0e7c-133">実稼働環境では、フレームワークの例外メッセージやスタックトレースを UI に表示しません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="b0e7c-134">例外メッセージまたはスタックトレースを表示すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="b0e7c-135">エンドユーザーに機密情報を開示します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="b0e7c-136">悪意のあるユーザーがアプリ、サーバー、またはネットワークのセキュリティを侵害する可能性のある脆弱性を検出するのを支援します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="b0e7c-137">永続的なプロバイダーでエラーをログに記録する</span><span class="sxs-lookup"><span data-stu-id="b0e7c-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="b0e7c-138">未処理の例外が発生した場合、例外は、サービスコンテナーに構成されている <xref:Microsoft.Extensions.Logging.ILogger> インスタンスに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="b0e7c-139">既定では、Blazor アプリはコンソールログプロバイダーを使用してコンソール出力に記録されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="b0e7c-140">ログサイズとログローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="b0e7c-141">詳細については、「 <xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="b0e7c-142">開発中、Blazor は通常、デバッグを支援するために、ブラウザーのコンソールに例外の完全な詳細情報を送信します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="b0e7c-143">運用環境では、ブラウザーのコンソールでの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細は引き続きサーバー側でログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="b0e7c-144">詳細については、「 <xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="b0e7c-145">ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="b0e7c-146">悪意のあるユーザーは、意図的にエラーをトリガーできる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="b0e7c-147">たとえば、製品の詳細を表示するコンポーネントの URL に不明な `ProductId` が指定されている場合は、エラーからインシデントをログに記録しないようにします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="b0e7c-148">すべてのエラーを、ログ記録の重要度の高いインシデントとして処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="b0e7c-149">エラーが発生する可能性のある場所</span><span class="sxs-lookup"><span data-stu-id="b0e7c-149">Places where errors may occur</span></span>

<span data-ttu-id="b0e7c-150">フレームワークとアプリコードは、次のいずれかの場所でハンドルされない例外をトリガーすることがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="b0e7c-151">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="b0e7c-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="b0e7c-152">ライフサイクルメソッド</span><span class="sxs-lookup"><span data-stu-id="b0e7c-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="b0e7c-153">レンダリングロジック</span><span class="sxs-lookup"><span data-stu-id="b0e7c-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="b0e7c-154">イベントハンドラー</span><span class="sxs-lookup"><span data-stu-id="b0e7c-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="b0e7c-155">コンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="b0e7c-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="b0e7c-156">JavaScript の相互運用</span><span class="sxs-lookup"><span data-stu-id="b0e7c-156">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="b0e7c-157">サーキットハンドラー</span><span class="sxs-lookup"><span data-stu-id="b0e7c-157">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="b0e7c-158">回線破棄</span><span class="sxs-lookup"><span data-stu-id="b0e7c-158">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="b0e7c-159">プリ</span><span class="sxs-lookup"><span data-stu-id="b0e7c-159">Prerendering</span></span>](#prerendering)

<span data-ttu-id="b0e7c-160">上記のハンドルされない例外については、この記事の次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="b0e7c-161">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="b0e7c-161">Component instantiation</span></span>

<span data-ttu-id="b0e7c-162">Blazor がコンポーネントのインスタンスを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="b0e7c-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="b0e7c-163">コンポーネントのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="b0e7c-164">[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)ディレクティブまたは[`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component)属性を使用して、コンポーネントのコンストラクターに渡される非シングルトン DI サービスのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="b0e7c-165">任意の `[Inject]` プロパティに対して実行されるコンストラクターまたは setter がハンドルされない例外をスローすると、回線が失敗します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-165">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="b0e7c-166">フレームワークはコンポーネントをインスタンス化できないため、例外は fatal です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="b0e7c-167">コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="b0e7c-168">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="b0e7c-168">Lifecycle methods</span></span>

<span data-ttu-id="b0e7c-169">コンポーネントの有効期間中、Blazor は次の[ライフサイクルメソッド](xref:blazor/lifecycle)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="b0e7c-170">あるライフサイクルメソッドが例外を同期的または非同期的にスローする場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="b0e7c-171">コンポーネントがライフサイクルメソッドのエラーに対処するには、エラー処理ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="b0e7c-172">次の例では、`OnParametersSetAsync` がメソッドを呼び出して製品を取得します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="b0e7c-173">`ProductRepository.GetProductByIdAsync` メソッドでスローされた例外は、`try-catch` ステートメントによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="b0e7c-174">`catch` ブロックを実行すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="b0e7c-175">`loadFailed` は `true`に設定されます。これは、ユーザーにエラーメッセージを表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="b0e7c-176">エラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="b0e7c-177">レンダリングロジック</span><span class="sxs-lookup"><span data-stu-id="b0e7c-177">Rendering logic</span></span>

<span data-ttu-id="b0e7c-178">`.razor` コンポーネントファイル内の宣言マークアップは、`BuildRenderTree`とC#呼ばれるメソッドにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="b0e7c-179">コンポーネントがレンダリングされると、`BuildRenderTree` が実行され、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造が構築されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="b0e7c-180">レンダリングロジックは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="b0e7c-181">このシナリオの例は、`@someObject.PropertyName` が評価され、`@someObject` が `null`場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="b0e7c-182">レンダリングロジックによってスローされた未処理の例外は、回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-182">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="b0e7c-183">レンダリングロジックで null 参照例外が発生しないようにするには、そのメンバーにアクセスする前に `null` オブジェクトを確認します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="b0e7c-184">次の例では、`person.Address` が `null`場合、`person.Address` のプロパティにはアクセスしません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="b0e7c-185">上記のコードでは、`person` が `null`でないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="b0e7c-186">多くの場合、コードの構造によって、コンポーネントのレンダリング時にオブジェクトが存在することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="b0e7c-187">そのような場合は、表示ロジックで `null` を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="b0e7c-188">前の例では、コンポーネントがインスタンス化されるときに `person` が作成されるため、`person` が確実に存在することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="b0e7c-189">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="b0e7c-189">Event handlers</span></span>

<span data-ttu-id="b0e7c-190">クライアント側のコードは、次C#を使用してイベントハンドラーが作成されるときに、コードの呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="b0e7c-191">その他の `@on...` 属性</span><span class="sxs-lookup"><span data-stu-id="b0e7c-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="b0e7c-192">これらのシナリオでは、イベントハンドラーコードによってハンドルされない例外がスローされることがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="b0e7c-193">イベントハンドラーがハンドルされない例外をスローした場合 (たとえば、データベースクエリが失敗した場合)、その例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="b0e7c-194">アプリが外部の理由で失敗する可能性のあるコードを呼び出した場合は、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="b0e7c-195">ユーザーコードによって例外がトラップされて処理されない場合は、フレームワークによって例外がログに記録され、回線が終了します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="b0e7c-196">コンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="b0e7c-196">Component disposal</span></span>

<span data-ttu-id="b0e7c-197">たとえば、ユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="b0e7c-198"><xref:System.IDisposable?displayProperty=fullName> を実装するコンポーネントが UI から削除されると、フレームワークはコンポーネントの <xref:System.IDisposable.Dispose*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="b0e7c-199">コンポーネントの `Dispose` メソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="b0e7c-200">破棄ロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch ](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用して例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="b0e7c-201">コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="b0e7c-202">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="b0e7c-202">JavaScript interop</span></span>

<span data-ttu-id="b0e7c-203">`IJSRuntime.InvokeAsync<T>` を使用すると、.NET コードは、ユーザーのブラウザーで JavaScript ランタイムへの非同期呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="b0e7c-204">`InvokeAsync<T>`でのエラー処理には、次の条件が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="b0e7c-205">`InvokeAsync<T>` の呼び出しが同期的に失敗した場合、.NET 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="b0e7c-206">たとえば、指定された引数をシリアル化できないため、`InvokeAsync<T>` を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="b0e7c-207">開発者コードは例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-207">Developer code must catch the exception.</span></span> <span data-ttu-id="b0e7c-208">イベントハンドラーまたはコンポーネントのライフサイクルメソッドのアプリコードで例外が処理されない場合、結果として得られる例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="b0e7c-209">`InvokeAsync<T>` の呼び出しが非同期に失敗した場合、.NET <xref:System.Threading.Tasks.Task> は失敗します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="b0e7c-210">たとえば、JavaScript 側のコードが例外をスローしたり、`rejected`として完了した `Promise` を返したりするために、`InvokeAsync<T>` の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="b0e7c-211">開発者コードは例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-211">Developer code must catch the exception.</span></span> <span data-ttu-id="b0e7c-212">[Await](/dotnet/csharp/language-reference/keywords/await)演算子を使用する場合は、エラー処理とログ記録を使用し[て、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでメソッド呼び出しをラップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="b0e7c-213">それ以外の場合、失敗したコードは、回路にとって致命的な未処理の例外を発生させることになります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-213">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="b0e7c-214">既定では、`InvokeAsync<T>` の呼び出しは特定の期間内に完了する必要があります。そうでない場合は、呼び出しがタイムアウトします。既定のタイムアウト期間は1分です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="b0e7c-215">タイムアウトは、完了メッセージを返信しないネットワーク接続または JavaScript コードの損失からコードを保護します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="b0e7c-216">呼び出しがタイムアウトした場合、結果として得られる `Task` は <xref:System.OperationCanceledException>で失敗します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="b0e7c-217">ログ記録で例外をトラップして処理します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="b0e7c-218">同様に、JavaScript コードでは、 [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)属性によって示される .net メソッドの呼び出しを開始できます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="b0e7c-219">これらの .NET メソッドでハンドルされない例外がスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="b0e7c-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="b0e7c-220">この例外は、回線にとって致命的な例外として扱われません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-220">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="b0e7c-221">JavaScript 側の `Promise` は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="b0e7c-222">.NET 側またはメソッド呼び出しの JavaScript 側でエラー処理コードを使用するオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="b0e7c-223">詳細については、「 <xref:blazor/javascript-interop>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="b0e7c-224">サーキットハンドラー</span><span class="sxs-lookup"><span data-stu-id="b0e7c-224">Circuit handlers</span></span>

Blazor<span data-ttu-id="b0e7c-225"> を使用すると、コードで*サーキットハンドラー*を定義し、ユーザーの回線の状態が変化したときに通知を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-225"> allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="b0e7c-226">次の状態が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-226">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="b0e7c-227">通知は、`CircuitHandler` 抽象基本クラスから継承する DI サービスを登録することによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-227">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="b0e7c-228">カスタムサーキットハンドラーのメソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-228">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="b0e7c-229">ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログ記録を含む1つまたは複数の[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでコードをラップします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-229">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="b0e7c-230">回線破棄</span><span class="sxs-lookup"><span data-stu-id="b0e7c-230">Circuit disposal</span></span>

<span data-ttu-id="b0e7c-231">ユーザーが切断され、フレームワークが回線の状態をクリーンアップしているために回線が終了すると、フレームワークは回線の DI スコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-231">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="b0e7c-232">スコープを破棄すると、<xref:System.IDisposable?displayProperty=fullName>を実装するサーキットスコープの DI サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-232">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="b0e7c-233">破棄中にいずれかの DI サービスがハンドルされない例外をスローした場合、フレームワークは例外をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-233">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="b0e7c-234">プリ</span><span class="sxs-lookup"><span data-stu-id="b0e7c-234">Prerendering</span></span>

Blazor<span data-ttu-id="b0e7c-235"> コンポーネントは、レンダリングされた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように、`Component` タグヘルパーを使用して prerendered できます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-235"> components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="b0e7c-236">これは次のように機能します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-236">This works by:</span></span>

* <span data-ttu-id="b0e7c-237">同じページに含まれるすべての prerendered コンポーネントに対して新しい回線を作成します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="b0e7c-238">初期 HTML を生成しています。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="b0e7c-239">ユーザーのブラウザーが同じサーバーへの SignalR 接続を確立するまで、回線を `disconnected` として扱います。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="b0e7c-240">接続が確立されると、回線の対話機能が再開され、コンポーネントの HTML マークアップが更新されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="b0e7c-241">たとえば、ライフサイクルメソッドやレンダリングロジックで、コンポーネントによって処理されない例外がスローされた場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="b0e7c-242">この例外は、回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="b0e7c-243">例外は、`Component` タグヘルパーからの呼び出し履歴によってスローされます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-243">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="b0e7c-244">したがって、例外が開発者コードによって明示的にキャッチされない限り、HTTP 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="b0e7c-245">通常の状況では、事前設定に失敗した場合に、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、作業コンポーネントをレンダリングできないためです。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="b0e7c-246">プリレンダリング中に発生する可能性のあるエラーを許容するには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="b0e7c-247">エラー処理とログ記録では、 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-247">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="b0e7c-248">`try-catch` ステートメントで `Component` タグヘルパーをラップするのではなく、`Component` タグヘルパーによって表示されるコンポーネントにエラー処理ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-248">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="b0e7c-249">高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="b0e7c-249">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="b0e7c-250">再帰的なレンダリング</span><span class="sxs-lookup"><span data-stu-id="b0e7c-250">Recursive rendering</span></span>

<span data-ttu-id="b0e7c-251">コンポーネントは再帰的に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-251">Components can be nested recursively.</span></span> <span data-ttu-id="b0e7c-252">これは、再帰的なデータ構造を表す場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-252">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="b0e7c-253">たとえば、`TreeNode` コンポーネントでは、ノードの子ごとにより多くの `TreeNode` コンポーネントをレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-253">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="b0e7c-254">再帰的にレンダリングする場合は、無限再帰になるようなコーディングパターンを避けます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-254">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="b0e7c-255">循環を含むデータ構造を再帰的に表示することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-255">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="b0e7c-256">たとえば、子を含むツリーノードは表示しません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-256">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="b0e7c-257">循環を含むレイアウトのチェーンを作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-257">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="b0e7c-258">たとえば、レイアウトがそれ自体であるレイアウトを作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-258">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="b0e7c-259">悪意のあるデータ入力または JavaScript の相互運用呼び出しによって、エンドユーザーが再帰不変 (ルール) に違反しないようにします。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-259">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="b0e7c-260">レンダリング中の無限ループ:</span><span class="sxs-lookup"><span data-stu-id="b0e7c-260">Infinite loops during rendering:</span></span>

* <span data-ttu-id="b0e7c-261">レンダリングプロセスを永久に続行します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-261">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="b0e7c-262">は、終了しないループを作成するのと同じです。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-262">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="b0e7c-263">これらのシナリオでは、影響を受ける回線がハングし、スレッドは通常次を試行します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-263">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="b0e7c-264">オペレーティングシステムで許可されている CPU 時間を無期限に使用します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-264">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="b0e7c-265">無制限の数のサーバーメモリを消費します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-265">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="b0e7c-266">メモリを無制限に使用することは、反復処理のたびに終了しないループによってコレクションにエントリが追加されるシナリオと同じです。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-266">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="b0e7c-267">無限の再帰パターンを回避するには、再帰的なレンダリングコードに適切な停止条件が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-267">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="b0e7c-268">カスタムレンダリングツリーのロジック</span><span class="sxs-lookup"><span data-stu-id="b0e7c-268">Custom render tree logic</span></span>

<span data-ttu-id="b0e7c-269">ほとんどの Blazor コンポーネントは、razor ファイルとして実装され、`RenderTreeBuilder` を操作して出力を表示するロジックを生成するためにコンパイルされ*ます。*</span><span class="sxs-lookup"><span data-stu-id="b0e7c-269">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="b0e7c-270">開発者は、手続きC#型コードを使用して `RenderTreeBuilder` ロジックを手動で実装できます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-270">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="b0e7c-271">詳細については、「 <xref:blazor/components#manual-rendertreebuilder-logic>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-271">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="b0e7c-272">手動のレンダーツリービルダーロジックの使用は、高度で安全ではないシナリオと見なされます。一般的なコンポーネントの開発にはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-272">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="b0e7c-273">`RenderTreeBuilder` コードが記述されている場合、開発者はコードの正確性を保証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-273">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="b0e7c-274">たとえば、開発者は次のことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-274">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="b0e7c-275">`OpenElement` と `CloseElement` の呼び出しが正しく調整されています。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-275">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="b0e7c-276">属性は正しい場所にのみ追加されます。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-276">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="b0e7c-277">手動のレンダーツリービルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティの脆弱性など、任意の未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-277">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="b0e7c-278">アセンブリコードまたは MSIL 命令を手作業で記述するのと同じレベルの複雑さと同じレベルの*危険*を考慮して、手動のレンダリングツリービルダーロジックを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b0e7c-278">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
