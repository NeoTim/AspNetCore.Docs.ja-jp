---
title: ASP.NET Core のエラーを処理する
author: rick-anderson
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 2e6aabda449a24496916c6ea9fcbd38062b54c04
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017455"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="c13be-103">ASP.NET Core のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="c13be-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="c13be-104">作成者: [Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c13be-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c13be-105">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="c13be-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="c13be-106">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c13be-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="c13be-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="c13be-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="c13be-108">([ダウンロード方法](xref:index#how-to-download-a-sample)。)この記事には、サンプル アプリでプリプロセッサ ディレクティブ (`#if`、`#endif`、`#define`) を設定して、異なるシナリオを有効にする方法についての説明が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c13be-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="c13be-109">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="c13be-109">Developer Exception Page</span></span>

<span data-ttu-id="c13be-110">"*開発者例外ページ*" には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="c13be-111">そのページは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="c13be-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c13be-112">アプリを開発[環境](xref:fundamentals/environments)で実行するときにページを有効にするには、`Startup.Configure` メソッドにコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c13be-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="c13be-113">例外をキャッチしたいミドルウェアの前に、<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> の呼び出しを配置します。</span><span class="sxs-lookup"><span data-stu-id="c13be-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="c13be-114">**アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c13be-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="c13be-115">アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。</span><span class="sxs-lookup"><span data-stu-id="c13be-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="c13be-116">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c13be-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c13be-117">このページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c13be-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="c13be-118">スタック トレース</span><span class="sxs-lookup"><span data-stu-id="c13be-118">Stack trace</span></span>
* <span data-ttu-id="c13be-119">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="c13be-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="c13be-120">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="c13be-120">Cookies (if any)</span></span>
* <span data-ttu-id="c13be-121">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="c13be-121">Headers</span></span>

<span data-ttu-id="c13be-122">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で開発者例外ページを表示するには、`DevEnvironment` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c13be-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="c13be-123">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="c13be-123">Exception handler page</span></span>

<span data-ttu-id="c13be-124">運用環境用のカスタム エラー処理ページを構成するには、例外処理ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="c13be-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="c13be-125">ミドルウェアでは次を行います。</span><span class="sxs-lookup"><span data-stu-id="c13be-125">The middleware:</span></span>

* <span data-ttu-id="c13be-126">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c13be-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="c13be-127">ページ用の、またはコントローラーが指定した別のパイプラインで、要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="c13be-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="c13be-128">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="c13be-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="c13be-129">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="c13be-130">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml*) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="c13be-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="c13be-131">MVC アプリの場合、プロジェクト テンプレートにはエラー アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c13be-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="c13be-132">アクション メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="c13be-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="c13be-133">`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドをマークしないでください。</span><span class="sxs-lookup"><span data-stu-id="c13be-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="c13be-134">明示的な動詞を使用すると、要求がメソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="c13be-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="c13be-135">認証されていないユーザーがエラー ビューを受信できるように、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="c13be-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="c13be-136">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="c13be-136">Access the exception</span></span>

<span data-ttu-id="c13be-137">エラー ハンドラー コントローラーまたはページ内で例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="c13be-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="c13be-138">機密性の高いエラー情報をクライアントに提供**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="c13be-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="c13be-139">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="c13be-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="c13be-140">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ページを表示するには、`ProdEnvironment` および `ErrorHandlerPage` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c13be-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="c13be-141">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="c13be-141">Exception handler lambda</span></span>

<span data-ttu-id="c13be-142">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="c13be-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="c13be-143">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="c13be-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="c13be-144">例外処理にラムダを使う例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c13be-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="c13be-145">上記のコードでは `await context.Response.WriteAsync(new string(' ', 512));` が追加されるため、Internet Explorer ブラウザーには IE のエラー メッセージではなく、このエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="c13be-146">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)します。</span><span class="sxs-lookup"><span data-stu-id="c13be-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="c13be-147"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="c13be-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="c13be-148">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="c13be-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="c13be-149">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ラムダの結果を表示するには、`ProdEnvironment` および `ErrorHandlerLambda` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c13be-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="c13be-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="c13be-150">UseStatusCodePages</span></span>

<span data-ttu-id="c13be-151">ASP.NET Core アプリでは、既定で、"*404 - 見つかりません*" などの HTTP 状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="c13be-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="c13be-152">アプリでは、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="c13be-153">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="c13be-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="c13be-154">そのミドルウェアは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内にある [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="c13be-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c13be-155">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c13be-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="c13be-156">要求処理ミドルウェア (たとえば、静的ファイル ミドルウェアや MVC ミドルウェア) の前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c13be-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="c13be-157">既定のハンドラーによって表示されるテキストの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c13be-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="c13be-158">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)でさまざまな状態コード ページ形式のいずれかを表示するには、`StatusCodePages` で始まるプリプロセッサ ディレクティブのいずれかを使用して、ホーム ページの **[Trigger a 404]\(404 をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c13be-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="c13be-159">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="c13be-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="c13be-160">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="c13be-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="c13be-161">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="c13be-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="c13be-162">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="c13be-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="c13be-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="c13be-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="c13be-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="c13be-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="c13be-165">クライアントに *302 - Found* 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="c13be-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="c13be-166">URL テンプレートで指定された場所にクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="c13be-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="c13be-167">次の例で示すように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c13be-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="c13be-168">URL テンプレートがチルダ (~) で始まっている場合、チルダはアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c13be-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="c13be-169">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="c13be-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="c13be-170">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c13be-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="c13be-171">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="c13be-172">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="c13be-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="c13be-173">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="c13be-174">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="c13be-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="c13be-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="c13be-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="c13be-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="c13be-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="c13be-177">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="c13be-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="c13be-178">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="c13be-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="c13be-179">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="c13be-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="c13be-180">`UseRouting` の前に `UseStatusCodePagesWithReExecute` が配置されていることを確認して、要求を状態ページに再ルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="c13be-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="c13be-181">Razor Pages の例については、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c13be-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="c13be-182">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="c13be-183">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="c13be-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="c13be-184">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="c13be-185">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="c13be-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="c13be-186">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー (`{0}`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c13be-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="c13be-187">URL テンプレートの先頭には、スラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="c13be-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="c13be-188">パスでプレースホルダーを使う場合は、エンドポイント (ページまたはコントローラー) でパスのセグメントを処理できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c13be-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="c13be-189">たとえば、エラー用の Razor ページでは、`@page` ディレクティブの付いた省略可能なパスのセグメント値を受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c13be-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="c13be-190">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="c13be-191">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="c13be-191">Disable status code pages</span></span>

<span data-ttu-id="c13be-192">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c13be-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="c13be-193">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="c13be-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="c13be-194">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="c13be-194">Exception-handling code</span></span>

<span data-ttu-id="c13be-195">例外処理ページのコードは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="c13be-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="c13be-196">実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c13be-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="c13be-197">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="c13be-197">Response headers</span></span>

<span data-ttu-id="c13be-198">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c13be-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="c13be-199">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="c13be-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="c13be-200">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="c13be-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="c13be-201">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c13be-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="c13be-202">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="c13be-202">Server exception handling</span></span>

<span data-ttu-id="c13be-203">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="c13be-204">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "*500 - 内部サーバー エラーです*" 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="c13be-205">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="c13be-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="c13be-206">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="c13be-207">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="c13be-208">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="c13be-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="c13be-209">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="c13be-209">Startup exception handling</span></span>

<span data-ttu-id="c13be-210">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="c13be-211">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c13be-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="c13be-212">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="c13be-213">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c13be-213">If binding fails:</span></span>

* <span data-ttu-id="c13be-214">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="c13be-215">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="c13be-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="c13be-216">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="c13be-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c13be-217">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から "*502.5 - 処理エラー*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="c13be-218">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c13be-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="c13be-219">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="c13be-219">Database error page</span></span>

<span data-ttu-id="c13be-220">データベース エラー ページ ミドルウェアでは、Entity Framework の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="c13be-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="c13be-221">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c13be-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="c13be-222">このページは、開発環境でのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c13be-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="c13be-223">ページを有効にするには、コードを `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="c13be-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="c13be-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> には、[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="c13be-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="c13be-225">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="c13be-225">Exception filters</span></span>

<span data-ttu-id="c13be-226">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="c13be-227">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="c13be-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="c13be-228">このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="c13be-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="c13be-229">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c13be-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="c13be-230">例外フィルターは、MVC アクション内で発生した例外をトラップする場合は便利ですが、例外処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="c13be-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="c13be-231">ミドルウェアの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c13be-231">We recommend using the middleware.</span></span> <span data-ttu-id="c13be-232">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合にのみ、フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="c13be-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="c13be-233">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="c13be-233">Model state errors</span></span>

<span data-ttu-id="c13be-234">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c13be-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c13be-235">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c13be-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
