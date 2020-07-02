---
title: ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化
author: rick-anderson
description: ASP.NET Core で、ファクトリ ベースの厳密に型指定されたミドルウェアをアクティブ化する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 893dce119328acaa4ffd3087b94328017f5915ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399908"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="89a60-103">ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化</span><span class="sxs-lookup"><span data-stu-id="89a60-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89a60-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="89a60-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="89a60-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="89a60-106">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="89a60-107">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="89a60-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="89a60-108">利点:</span><span class="sxs-lookup"><span data-stu-id="89a60-108">Benefits:</span></span>

* <span data-ttu-id="89a60-109">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="89a60-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="89a60-110">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="89a60-110">Strong typing of middleware</span></span>

<span data-ttu-id="89a60-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="89a60-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="89a60-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="89a60-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="89a60-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="89a60-113">IMiddleware</span></span>

<span data-ttu-id="89a60-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="89a60-115">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="89a60-116">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="89a60-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="89a60-117"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="89a60-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="89a60-118">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="89a60-119"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="89a60-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="89a60-120">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="89a60-121">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="89a60-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="89a60-122">IMiddlewareFactory</span></span>

<span data-ttu-id="89a60-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="89a60-124">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="89a60-125">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="89a60-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89a60-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> は、[ミドルウェア](xref:fundamentals/middleware/index)のアクティブ化の拡張ポイントです。</span><span class="sxs-lookup"><span data-stu-id="89a60-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="89a60-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="89a60-128">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="89a60-129">ミドルウェアは、アプリのサービス コンテナーで、[スコープ サービスまたは一時的サービス](xref:fundamentals/dependency-injection#service-lifetimes)として登録されています。</span><span class="sxs-lookup"><span data-stu-id="89a60-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="89a60-130">利点:</span><span class="sxs-lookup"><span data-stu-id="89a60-130">Benefits:</span></span>

* <span data-ttu-id="89a60-131">クライアント要求ごとにアクティブ化 (スコープ サービスの挿入)</span><span class="sxs-lookup"><span data-stu-id="89a60-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="89a60-132">ミドルウェアの厳密な型指定</span><span class="sxs-lookup"><span data-stu-id="89a60-132">Strong typing of middleware</span></span>

<span data-ttu-id="89a60-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> はクライアント要求 (接続) ごとにアクティブ化されているので、スコープ サービスをミドルウェアのコンストラクターに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="89a60-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="89a60-134">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="89a60-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="89a60-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="89a60-135">IMiddleware</span></span>

<span data-ttu-id="89a60-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> では、アプリの要求パイプライン用にミドルウェアが定義されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="89a60-137">要求は、[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) メソッドによって処理され、ミドルウェアの実行を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="89a60-138">規則でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="89a60-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="89a60-139"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory> でアクティブ化されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="89a60-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="89a60-140">ミドルウェア用に拡張機能が作成されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="89a60-141"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> では、ファクトリでアクティブ化されたミドルウェアにオブジェクトを渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="89a60-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="89a60-142">ファクトリでアクティブ化されたミドルウェアは、`Startup.ConfigureServices` の組み込みのコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="89a60-143">この 2 つのミドルウェアは、要求を処理する `Startup.Configure` のパイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="89a60-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="89a60-144">IMiddlewareFactory</span></span>

<span data-ttu-id="89a60-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> では、ミドルウェアを作成するメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="89a60-146">ミドルウェア ファクトリの実装は、スコープ化されたサービスとして、コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="89a60-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="89a60-147">既定の <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> の実装である <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> は、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="89a60-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="89a60-148">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="89a60-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
