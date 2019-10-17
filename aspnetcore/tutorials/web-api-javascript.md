---
title: 'チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す'
author: rick-anderson
description: JavaScript を使用して ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: bbe261307f6f68af002cb98cc4895888ade7f61c
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378703"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="f934d-103">チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="f934d-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="f934d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f934d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f934d-105">このチュートリアルでは、[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して JavaScript で ASP.NET Core Web API を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f934d-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f934d-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f934d-106">Prerequisites</span></span>

* <span data-ttu-id="f934d-107">[Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること</span><span class="sxs-lookup"><span data-stu-id="f934d-107">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="f934d-108">CSS、HTML、JavaScript に関する知識</span><span class="sxs-lookup"><span data-stu-id="f934d-108">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f934d-109">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="f934d-109">Call the web API with JavaScript</span></span>

<span data-ttu-id="f934d-110">このセクションでは、To Do アイテムを作成および管理するためのフォームが含まれる HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="f934d-110">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="f934d-111">イベント ハンドラーを、ページ上の要素にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="f934d-111">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="f934d-112">イベント ハンドラーによって、Web API のアクション メソッドに HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-112">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="f934d-113">Fetch API の `fetch` 関数により、各 HTTP 要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-113">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="f934d-114">`fetch` 関数からは `Promise` オブジェクトが返され、このオブジェクトには `Response` オブジェクトとして表された HTTP 応答が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f934d-114">The `fetch` function returns a `Promise` object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="f934d-115">一般的なパターンでは、`Response` オブジェクトに対して `json` 関数を呼び出して、JSON 応答の本文を抽出します。</span><span class="sxs-lookup"><span data-stu-id="f934d-115">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="f934d-116">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-116">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f934d-117">`fetch` の最も簡単な呼び出しでは、ルートを表す 1 つのパラメーターが受け付けられます。</span><span class="sxs-lookup"><span data-stu-id="f934d-117">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="f934d-118">`init` オブジェクトである 2 番目のパラメーターは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="f934d-118">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="f934d-119">`init` は、HTTP 要求を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-119">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="f934d-120">[静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ように、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="f934d-120">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f934d-121">*Startup.cs* の `Configure` メソッドでは、次の強調表示されたコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="f934d-121">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="f934d-122">プロジェクト ルートに *wwwroot* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="f934d-122">Create a *wwwroot* directory in the project root.</span></span>

1. <span data-ttu-id="f934d-123">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="f934d-123">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f934d-124">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f934d-124">Replace its contents with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="f934d-125">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="f934d-125">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f934d-126">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f934d-126">Replace its contents with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f934d-127">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f934d-127">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="f934d-128">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="f934d-128">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="f934d-129">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="f934d-129">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f934d-130">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f934d-130">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f934d-131">以下では、Web API 要求について説明します。</span><span class="sxs-lookup"><span data-stu-id="f934d-131">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f934d-132">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="f934d-132">Get a list of to-do items</span></span>

<span data-ttu-id="f934d-133">次のコードでは、HTTP GET 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-133">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="f934d-134">Web API から正常状態コードが返されると、`_displayItems` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-134">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="f934d-135">`_displayItems` によって受け入れられた配列パラメーターの各 To Do アイテムが、 **[Edit]** ボタンと **[Delete]** ボタンのあるテーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-135">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="f934d-136">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-136">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="f934d-137">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="f934d-137">Add a to-do item</span></span>

<span data-ttu-id="f934d-138">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f934d-138">In the following code:</span></span>

* <span data-ttu-id="f934d-139">`item` 変数は、To Do アイテムのオブジェクト リテラル表現を構築するために宣言されています。</span><span class="sxs-lookup"><span data-stu-id="f934d-139">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="f934d-140">フェッチ要求は、次のオプションで構成されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-140">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="f934d-141">`method` &mdash; POST HTTP アクション動詞が指定されています。</span><span class="sxs-lookup"><span data-stu-id="f934d-141">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="f934d-142">`body` &mdash; 要求本文の JSON 表現が指定されています。</span><span class="sxs-lookup"><span data-stu-id="f934d-142">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="f934d-143">JSON は、`item` に格納されているオブジェクト リテラルを [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 関数に渡すことによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-143">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="f934d-144">`headers` &mdash; `Accept` および `Content-Type` の HTTP 要求ヘッダーが指定されています。</span><span class="sxs-lookup"><span data-stu-id="f934d-144">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="f934d-145">どちらのヘッダーも `application/json` に設定され、それぞれ、受信および送信されるメディアの種類が指定されています。</span><span class="sxs-lookup"><span data-stu-id="f934d-145">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="f934d-146">HTTP POST 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-146">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="f934d-147">Web API で正常状態コードが返されると、`getItems` 関数が呼び出されて、HTML テーブルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-147">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="f934d-148">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="f934d-148">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="f934d-149">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="f934d-149">Update a to-do item</span></span>

<span data-ttu-id="f934d-150">To Do アイテムの更新は追加と似ていますが、2 つの大きな違いがあります。</span><span class="sxs-lookup"><span data-stu-id="f934d-150">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="f934d-151">ルートに、更新するアイテムの一意の識別子がサフィックスとして付けられます。</span><span class="sxs-lookup"><span data-stu-id="f934d-151">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="f934d-152">たとえば、*api/TodoItems/1* のようになります。</span><span class="sxs-lookup"><span data-stu-id="f934d-152">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="f934d-153">`method` オプションで示されているように、HTTP アクション動詞は PUT です。</span><span class="sxs-lookup"><span data-stu-id="f934d-153">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f934d-154">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="f934d-154">Delete a to-do item</span></span>

<span data-ttu-id="f934d-155">To Do アイテムを削除するには、要求の `method` オプションを `DELETE` に設定し、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="f934d-155">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="f934d-156">Web API のヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="f934d-156">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
