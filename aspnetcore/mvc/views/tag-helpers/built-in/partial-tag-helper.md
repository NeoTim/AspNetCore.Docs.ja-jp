---
title: ASP.NET Core の部分タグ ヘルパー
author: scottaddie
description: ASP.NET Core 部分タグ ヘルパーと、その各属性が部分ビューのレンダリングにおいて果たす役割について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 4a9f1958cd26bb57fcf3944aabacd57c470fb17e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626949"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="10b7e-103">ASP.NET Core の部分タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="10b7e-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="10b7e-104">作成者: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="10b7e-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="10b7e-105">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="10b7e-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="10b7e-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="10b7e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="10b7e-107">概要</span><span class="sxs-lookup"><span data-stu-id="10b7e-107">Overview</span></span>

<span data-ttu-id="10b7e-108">部分タグヘルパーは、ページおよび MVC アプリで [部分ビュー](xref:mvc/views/partial) を表示するために使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="10b7e-109">考慮事項:</span><span class="sxs-lookup"><span data-stu-id="10b7e-109">Consider that it:</span></span>

* <span data-ttu-id="10b7e-110">ASP.NET Core 2.1 以降を必要とします。</span><span class="sxs-lookup"><span data-stu-id="10b7e-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="10b7e-111">[HTML ヘルパー構文](xref:mvc/views/partial#reference-a-partial-view)の代替です。</span><span class="sxs-lookup"><span data-stu-id="10b7e-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="10b7e-112">部分ビューを非同期でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="10b7e-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="10b7e-113">部分ビューをレンダリングするための HTML ヘルパー オプション:</span><span class="sxs-lookup"><span data-stu-id="10b7e-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="10b7e-114">*Product* モデルがこのドキュメント全体でサンプルとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="10b7e-115">部分タグ ヘルパー属性のインベントリが続きます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="10b7e-116">name</span><span class="sxs-lookup"><span data-stu-id="10b7e-116">name</span></span>

<span data-ttu-id="10b7e-117">`name` 属性は必須です。</span><span class="sxs-lookup"><span data-stu-id="10b7e-117">The `name` attribute is required.</span></span> <span data-ttu-id="10b7e-118">レンダリングする部分ビューの名前またはパスを示します。</span><span class="sxs-lookup"><span data-stu-id="10b7e-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="10b7e-119">部分ビュー名が指定されると、[ビューの検出](xref:mvc/views/overview#view-discovery)プロセスが開始します。</span><span class="sxs-lookup"><span data-stu-id="10b7e-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="10b7e-120">明示的なパスが指定されているとき、このプロセスは回避されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="10b7e-121">許容されるすべての `name` 値については、「[部分ビューの検出](xref:mvc/views/partial#partial-view-discovery)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="10b7e-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="10b7e-122">次のマークアップでは明示的なパスが使用されており、*_ProductPartial.cshtml* が *Shared* フォルダーから読み込まれることを示しています。</span><span class="sxs-lookup"><span data-stu-id="10b7e-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="10b7e-123">[for](#for) 属性を使用し、バインディングのために部分ビューにモデルが渡されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="10b7e-124">対象</span><span class="sxs-lookup"><span data-stu-id="10b7e-124">for</span></span>

<span data-ttu-id="10b7e-125">`for` 属性によって、現在のモデルに対して評価する [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="10b7e-126">`ModelExpression` によって `@Model.` 構文が推論されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="10b7e-127">たとえば、`for="Product"` の代わりに `for="@Model.Product"` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="10b7e-128">この既定の推論動作は、`@` シンボルを使用してインライン式を定義することでオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="10b7e-129">次のマークアップでは、*_ProductPartial.cshtml* が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-129">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="10b7e-130">部分ビューは、関連ページ モデルの `Product` プロパティにバインドされています。</span><span class="sxs-lookup"><span data-stu-id="10b7e-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="10b7e-131">model</span><span class="sxs-lookup"><span data-stu-id="10b7e-131">model</span></span>

<span data-ttu-id="10b7e-132">`model` 属性によって、部分ビューに渡すモデル インスタンスが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="10b7e-133">`model` 属性は [for](#for) 属性と共に使用できません。</span><span class="sxs-lookup"><span data-stu-id="10b7e-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="10b7e-134">次のマークアップでは、新しい `Product` オブジェクトがインスタンス化され、バインディングのために `model` 属性に渡されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="10b7e-135">view-data</span><span class="sxs-lookup"><span data-stu-id="10b7e-135">view-data</span></span>

<span data-ttu-id="10b7e-136">`view-data` 属性によって、部分ビューに渡す [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="10b7e-137">次のマークアップでは、ViewData コレクション全体が部分ビューで使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="10b7e-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="10b7e-138">前述のコードでは、`IsNumberReadOnly` キーの値が `true` に設定されており、ViewData コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="10b7e-139">その結果、次の部分ビュー内で `ViewData["IsNumberReadOnly"]` を利用できます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="10b7e-140">この例では、`ViewData["IsNumberReadOnly"]` の値によって、*Number* フィールドを読み取り専用として表示するかどうかが決定されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="10b7e-141">HTML ヘルパーから移行する</span><span class="sxs-lookup"><span data-stu-id="10b7e-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="10b7e-142">次のような非同期の HTML ヘルパーの例を考えてみてください。</span><span class="sxs-lookup"><span data-stu-id="10b7e-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="10b7e-143">製品のコレクションが反復処理され、表示されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="10b7e-144">`PartialAsync` メソッドの最初のパラメーターごとに、*_ProductPartial.cshtml* の部分ビューが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="10b7e-145">`Product` モデルのインスタンスが、バインディングのために部分ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="10b7e-146">次の部分タグ ヘルパーは、`PartialAsync` HTML ヘルパーと同じ非同期レンダリング動作を実現します。</span><span class="sxs-lookup"><span data-stu-id="10b7e-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="10b7e-147">部分ビューのバインディングのため、`model` 属性に `Product` モデルのインスタンスが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="10b7e-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="10b7e-148">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="10b7e-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
