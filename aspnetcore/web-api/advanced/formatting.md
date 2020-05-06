---
title: ASP.NET Core Web API の応答データの書式設定
author: ardalis
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 22787b20879c3739ee8a8d74c7a39e7cf8f4d5b0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774237"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="fec23-103">ASP.NET Core Web API の応答データの書式設定</span><span class="sxs-lookup"><span data-stu-id="fec23-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="fec23-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fec23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fec23-105">ASP.NET Core MVC では、応答データの書式設定がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="fec23-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="fec23-106">応答データは、特定の形式を使用して、またはクライアントが要求した形式に応じて書式設定できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="fec23-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fec23-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="fec23-108">書式固有アクションの結果</span><span class="sxs-lookup"><span data-stu-id="fec23-108">Format-specific Action Results</span></span>

<span data-ttu-id="fec23-109">アクションの結果には、<xref:Microsoft.AspNetCore.Mvc.JsonResult> や <xref:Microsoft.AspNetCore.Mvc.ContentResult> のように、特定の形式に固有となる型があります。</span><span class="sxs-lookup"><span data-stu-id="fec23-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="fec23-110">アクションでは、クライアントの設定に関係なく、特定の形式で書式設定された結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="fec23-111">たとえば、`JsonResult` を返すと、JSON 形式のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="fec23-112">`ContentResult` または文字列を返すと、プレーンテキスト形式の文字列データが返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="fec23-113">アクションが特定の型を返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="fec23-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="fec23-114">ASP.NET Core によって、すべてのオブジェクトの戻り値がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="fec23-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="fec23-115">型が <xref:Microsoft.AspNetCore.Mvc.IActionResult> ではないオブジェクトを返すアクションからの結果は、適切な <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 実装を利用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="fec23-116">詳細については、「<xref:web-api/action-return-types>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fec23-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="fec23-117">組み込みヘルパー メソッド <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> では、JSON 形式のデータが返されます。[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="fec23-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="fec23-118">サンプル ダウンロードでは作成者の一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="fec23-119">F12 ブラウザー開発者ツールまたは [Postman](https://www.getpostman.com/tools) と前のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="fec23-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="fec23-120">**content-type:** `application/json; charset=utf-8` を含む応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="fec23-121">要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-121">The request headers are displayed.</span></span> <span data-ttu-id="fec23-122">たとえば、`Accept` ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="fec23-122">For example, the `Accept` header.</span></span> <span data-ttu-id="fec23-123">`Accept` ヘッダーは前のコードでは無視されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="fec23-124">プレーンテキストで書式設定されたデータを返すには、<xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> と <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> ヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="fec23-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="fec23-125">前のコードで、返される `Content-Type` は `text/plain` です。</span><span class="sxs-lookup"><span data-stu-id="fec23-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="fec23-126">文字列を返すと、`text/plain` の `Content-Type` が送られます。</span><span class="sxs-lookup"><span data-stu-id="fec23-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="fec23-127">戻り値の型が複数あるアクションの場合は、`IActionResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="fec23-128">たとえば、実行された操作の結果に基づいて、さまざまな HTTP 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="fec23-129">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="fec23-129">Content negotiation</span></span>

<span data-ttu-id="fec23-130">クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーションが発生します。</span><span class="sxs-lookup"><span data-stu-id="fec23-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="fec23-131">ASP.NET Core で使用される既定の形式は [JSON](https://json.org/) です。</span><span class="sxs-lookup"><span data-stu-id="fec23-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="fec23-132">コンテンツ ネゴシエーションの説明を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-132">Content negotiation is:</span></span>

* <span data-ttu-id="fec23-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult> によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="fec23-134">ヘルパー メソッドから返されるステータス コード固有のアクションの結果に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fec23-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="fec23-135">アクションの結果のヘルパー メソッドは `ObjectResult` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="fec23-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="fec23-136">モデルの型類が返されると、戻り値の型は `ObjectResult` になります。</span><span class="sxs-lookup"><span data-stu-id="fec23-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="fec23-137">次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="fec23-138">ASP.NET Core では、規定で `application/json`、`text/json`、`text/plain` のメディアの種類がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="fec23-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="fec23-139">[Fiddler](https://www.telerik.com/fiddler) や [Postman](https://www.getpostman.com/tools) のようなツールでは、`Accept` 要求ヘッダーを設定して戻り値の形式を指定できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="fec23-140">サーバーでサポートされている型が `Accept` ヘッダーに含まれている場合は、その型が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="fec23-141">フォーマッタの追加方法は次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="fec23-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="fec23-142">コントローラー アクションは POCO (単純な従来の CLR オブジェクト) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="fec23-143">POCO が返されると、ランタイムはそのオブジェクトをラップする `ObjectResult` を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="fec23-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="fec23-144">クライアントは、書式設定されたシリアル化オブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="fec23-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="fec23-145">返されるオブジェクトが `null` の場合は、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="fec23-146">オブジェクトの型を返す:</span><span class="sxs-lookup"><span data-stu-id="fec23-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="fec23-147">前のコードでは、有効な作成者エイリアスの要求に対して、`200 OK` という応答と作成者のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="fec23-148">無効なエイリアスの要求に対しては、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="fec23-149">Accept ヘッダー</span><span class="sxs-lookup"><span data-stu-id="fec23-149">The Accept header</span></span>

<span data-ttu-id="fec23-150">コンテンツ "*ネゴシエーション*" は、`Accept` ヘッダーが要求に含まれるときに発生します。</span><span class="sxs-lookup"><span data-stu-id="fec23-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="fec23-151">要求に Accept ヘッダーが含まれるとき、ASP.NET Core は次のように処理します。</span><span class="sxs-lookup"><span data-stu-id="fec23-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="fec23-152">Accept ヘッダーのメディアの種類を優先順で列挙します。</span><span class="sxs-lookup"><span data-stu-id="fec23-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="fec23-153">指定された形式のいずれかで応答を生成できるフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="fec23-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="fec23-154">クライアントの要求を満たすことができるフォーマッタが見つからない場合は、ASP.NET Core は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fec23-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="fec23-155"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> が設定されていた場合は、`406 Not Acceptable` を返します。それ以外の場合は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fec23-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="fec23-156">応答を生成できる最初のフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="fec23-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="fec23-157">要求された形式に対応するフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="fec23-158">要求に `Accept` ヘッダーが表示されない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="fec23-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="fec23-159">オブジェクトを処理できる最初のフォーマッタが使用されて、応答をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="fec23-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="fec23-160">ネゴシエーションは発生しません。</span><span class="sxs-lookup"><span data-stu-id="fec23-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="fec23-161">サーバーが返す形式を決定します。</span><span class="sxs-lookup"><span data-stu-id="fec23-161">The server is determining what format to return.</span></span>

<span data-ttu-id="fec23-162">Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が <xref:Microsoft.AspNetCore.Mvc.MvcOptions> で true に設定されていない限り、ヘッダーが無視されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="fec23-163">ブラウザーとコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="fec23-163">Browsers and content negotiation</span></span>

<span data-ttu-id="fec23-164">一般的な API クライアントとは異なり、Web ブラウザーは `Accept` ヘッダーを提供します。</span><span class="sxs-lookup"><span data-stu-id="fec23-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="fec23-165">Web ブラウザーでは、ワイルドカードを含む多くの形式が指定されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="fec23-166">既定では、要求がブラウザーから送信されていることをフレームワークが検出すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="fec23-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="fec23-167">`Accept` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="fec23-168">特に構成されていない限り、コンテンツは JSON で返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="fec23-169">このため、API を使用するときにブラウザー間でさらに一貫性の高いエクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="fec23-170">ブラウザーの Accept ヘッダーを優先するようにアプリを構成するには、<xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fec23-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="fec23-171">フォーマッタの構成</span><span class="sxs-lookup"><span data-stu-id="fec23-171">Configure formatters</span></span>

<span data-ttu-id="fec23-172">追加の形式をサポートする必要があるアプリは、適切な NuGet パッケージを追加し、サポートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="fec23-173">入力と出力で別々のフォーマッタがあります。</span><span class="sxs-lookup"><span data-stu-id="fec23-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="fec23-174">入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="fec23-175">出力フォーマッタは、応答の書式設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="fec23-176">カスタム フォーマッタの作成の詳細については、[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fec23-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="fec23-177">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="fec23-177">Add XML format support</span></span>

<span data-ttu-id="fec23-178"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="fec23-179">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="fec23-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="fec23-180">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="fec23-181">System.Text.Json ベースのフォーマッタを構成する</span><span class="sxs-lookup"><span data-stu-id="fec23-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="fec23-182">`System.Text.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="fec23-183">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="fec23-184">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="fec23-185">Newtonsoft.Json ベースの JSON 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="fec23-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="fec23-186">ASP.NET Core 3.0 より前、既定では、`Newtonsoft.Json` パッケージを使用して実装された JSON フォーマッタが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="fec23-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="fec23-187">ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="fec23-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="fec23-188">`Newtonsoft.Json` ベースのフォーマッタと機能のサポートは、[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet パッケージをインストールして `Startup.ConfigureServices` で構成することで利用できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="fec23-189">一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、`Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="fec23-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="fec23-190">アプリが以下の場合には、`Newtonsoft.Json` ベースのフォーマッタの使用を続けます。</span><span class="sxs-lookup"><span data-stu-id="fec23-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="fec23-191">`Newtonsoft.Json` 属性を使用する。</span><span class="sxs-lookup"><span data-stu-id="fec23-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="fec23-192">たとえば、`[JsonProperty]` または `[JsonIgnore]` です。</span><span class="sxs-lookup"><span data-stu-id="fec23-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="fec23-193">シリアル化の設定をカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="fec23-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="fec23-194">`Newtonsoft.Json` で提供される機能に依存する。</span><span class="sxs-lookup"><span data-stu-id="fec23-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="fec23-195">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。</span><span class="sxs-lookup"><span data-stu-id="fec23-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="fec23-196">ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="fec23-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="fec23-197">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。</span><span class="sxs-lookup"><span data-stu-id="fec23-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="fec23-198">`Newtonsoft.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="fec23-199">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="fec23-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="fec23-200">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="fec23-201">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="fec23-201">Add XML format support</span></span>

<span data-ttu-id="fec23-202">XML の書式設定には、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="fec23-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="fec23-203"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="fec23-204">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="fec23-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="fec23-205">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="fec23-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="fec23-206">形式を指定する</span><span class="sxs-lookup"><span data-stu-id="fec23-206">Specify a format</span></span>

<span data-ttu-id="fec23-207">応答形式を制限するには、 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute)フィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="fec23-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="fec23-208">ほとんどの[フィルター](xref:mvc/controllers/filters)と`[Produces]`同様に、アクション、コントローラー、またはグローバルスコープで適用できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="fec23-209">上記[`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute)のフィルター:</span><span class="sxs-lookup"><span data-stu-id="fec23-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="fec23-210">コントローラー内のすべてのアクションが、JSON で書式設定された応答を返すように強制します。</span><span class="sxs-lookup"><span data-stu-id="fec23-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="fec23-211">他のフォーマッタが構成されていて、クライアントが別の形式を指定した場合でも、JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="fec23-212">詳細については、[フィルター](xref:mvc/controllers/filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fec23-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="fec23-213">特殊なケースのフォーマッタ</span><span class="sxs-lookup"><span data-stu-id="fec23-213">Special case formatters</span></span>

<span data-ttu-id="fec23-214">一部の特殊なケースが組み込みのフォーマッタで実装されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="fec23-215">既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html*)。</span><span class="sxs-lookup"><span data-stu-id="fec23-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="fec23-216">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="fec23-217">フォーマッタは `ConfigureServices` メソッドで削除します。</span><span class="sxs-lookup"><span data-stu-id="fec23-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="fec23-218">戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、`204 No Content` を返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="fec23-219">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="fec23-220">次のコードでは、`StringOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="fec23-221">`StringOutputFormatter` がない場合は、組み込みの JSON フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="fec23-222">組み込みの JSON フォーマッタが削除され、XML フォーマッタを使用できる場合は、XML フォーマッタによって戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="fec23-223">それ以外の場合は、戻り値の型 `string` で `406 Not Acceptable` が返されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="fec23-224">`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="fec23-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="fec23-225">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-225">For example:</span></span>

* <span data-ttu-id="fec23-226">JSON フォーマッタは、本文が `null` の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="fec23-227">XML フォーマッタは、属性 `xsi:nil="true"` が設定された空の XML 要素を返します。</span><span class="sxs-lookup"><span data-stu-id="fec23-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="fec23-228">応答形式の URL マッピング</span><span class="sxs-lookup"><span data-stu-id="fec23-228">Response format URL mappings</span></span>

<span data-ttu-id="fec23-229">クライアントは、URL の一部として特定の形式を要求できます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="fec23-230">クエリ文字列またはパスの一部。</span><span class="sxs-lookup"><span data-stu-id="fec23-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="fec23-231">.xml または .json など形式固有のファイル拡張子の使用。</span><span class="sxs-lookup"><span data-stu-id="fec23-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="fec23-232">要求パスからのマッピングは、API で使用されるルートに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fec23-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="fec23-233">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fec23-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="fec23-234">前のルートを使用すると、要求された形式をオプションのファイル拡張子として指定できます。</span><span class="sxs-lookup"><span data-stu-id="fec23-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="fec23-235">属性[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)は、の`RouteData` format 値が存在するかどうかをチェックし、応答が作成されるときに応答形式を適切なフォーマッタにマップします。</span><span class="sxs-lookup"><span data-stu-id="fec23-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="fec23-236">ルート</span><span class="sxs-lookup"><span data-stu-id="fec23-236">Route</span></span>        |             <span data-ttu-id="fec23-237">フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="fec23-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="fec23-238">既定の出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="fec23-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="fec23-239">JSON フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="fec23-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="fec23-240">XML フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="fec23-240">The XML formatter (if configured)</span></span>  |
