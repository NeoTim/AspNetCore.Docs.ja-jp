---
title: ASP.NET 4.x と ASP.NET Core の選択
author: rick-anderson
description: ASP.NET Core とASP.NET 4.x の違いと、どちらかを選択する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: e0b0311622a841183433363a55fcf33e22b6b115
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633475"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="aef29-103">ASP.NET 4.x と ASP.NET Core の選択</span><span class="sxs-lookup"><span data-stu-id="aef29-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="aef29-104">ASP.NET Core は ASP.NET 4.x を再設計したものです。</span><span class="sxs-lookup"><span data-stu-id="aef29-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="aef29-105">この記事では、この 2 つの違いを一覧します。</span><span class="sxs-lookup"><span data-stu-id="aef29-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="aef29-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aef29-106">ASP.NET Core</span></span>

<span data-ttu-id="aef29-107">ASP.NET Core は、Windows、macOS、または Linux で最新のクラウド ベースの Web アプリを構築するための、オープン ソースのクロスプラットフォーム フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="aef29-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="aef29-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="aef29-108">ASP.NET 4.x</span></span>

<span data-ttu-id="aef29-109">ASP.NET 4.x は成熟したフレームワークであり、Windows 上でエンタープライズ グレードのサーバー ベース Web アプリを構築するために必要なサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="aef29-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="aef29-110">フレームワークの選択</span><span class="sxs-lookup"><span data-stu-id="aef29-110">Framework selection</span></span>

<span data-ttu-id="aef29-111">次の表では、ASP.NET Core と ASP.NET 4.x を比較します。</span><span class="sxs-lookup"><span data-stu-id="aef29-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="aef29-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aef29-112">ASP.NET Core</span></span> | <span data-ttu-id="aef29-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="aef29-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="aef29-114">Windows、macOS、Linux が対象</span><span class="sxs-lookup"><span data-stu-id="aef29-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="aef29-115">Windows が対象</span><span class="sxs-lookup"><span data-stu-id="aef29-115">Build for Windows</span></span>|
|<span data-ttu-id="aef29-116">[Razor Pages](xref:razor-pages/index) は、ASP.NET Core 2.x の時点で Web UI を作成する場合に推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="aef29-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="aef29-117">[MVC](xref:mvc/overview)、[Web API](xref:tutorials/first-web-api)、[SignalR](xref:signalr/introduction) についても参照してください。</span><span class="sxs-lookup"><span data-stu-id="aef29-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="aef29-118">[Web Forms](/aspnet/web-forms)、[SignalR](/aspnet/signalr)、[MVC](/aspnet/mvc)、[Web API](/aspnet/web-api/)、[WebHooks](/aspnet/webhooks/)、または [Web ページ](/aspnet/web-pages)を使います</span><span class="sxs-lookup"><span data-stu-id="aef29-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="aef29-119">コンピューターごとに複数のバージョン</span><span class="sxs-lookup"><span data-stu-id="aef29-119">Multiple versions per machine</span></span>|<span data-ttu-id="aef29-120">コンピューターごとに 1 つのバージョン</span><span class="sxs-lookup"><span data-stu-id="aef29-120">One version per machine</span></span>|
|<span data-ttu-id="aef29-121">C# または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)、または [Visual Studio Code](https://code.visualstudio.com/) で 開発</span><span class="sxs-lookup"><span data-stu-id="aef29-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="aef29-122">C#、VB、または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/) で開発</span><span class="sxs-lookup"><span data-stu-id="aef29-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="aef29-123">ASP.NET 4.x より高いパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="aef29-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="aef29-124">よいパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="aef29-124">Good performance</span></span>|
|[<span data-ttu-id="aef29-125">.NET Core ランタイムを使用する</span><span class="sxs-lookup"><span data-stu-id="aef29-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="aef29-126">.NET Framework ランタイムを使います</span><span class="sxs-lookup"><span data-stu-id="aef29-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="aef29-127">.NET Framework 上での ASP.NET Core 2.x のサポートについては、「[.NET Framework を対象とする ASP.NET Core](xref:index#target-framework)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aef29-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="aef29-128">ASP.NET Core のシナリオ</span><span class="sxs-lookup"><span data-stu-id="aef29-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="aef29-129">Web サイト</span><span class="sxs-lookup"><span data-stu-id="aef29-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="aef29-130">API</span><span class="sxs-lookup"><span data-stu-id="aef29-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="aef29-131">リアルタイム</span><span class="sxs-lookup"><span data-stu-id="aef29-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="aef29-132">Azure に ASP.NET Core アプリをデプロイする</span><span class="sxs-lookup"><span data-stu-id="aef29-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="aef29-133">ASP.NET 4.x のシナリオ</span><span class="sxs-lookup"><span data-stu-id="aef29-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="aef29-134">Web サイト</span><span class="sxs-lookup"><span data-stu-id="aef29-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="aef29-135">API</span><span class="sxs-lookup"><span data-stu-id="aef29-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="aef29-136">リアルタイム</span><span class="sxs-lookup"><span data-stu-id="aef29-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="aef29-137">Azure 内で ASP.NET 4.x Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="aef29-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="aef29-138">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aef29-138">Additional resources</span></span>

* [<span data-ttu-id="aef29-139">ASP.NET の概要</span><span class="sxs-lookup"><span data-stu-id="aef29-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="aef29-140">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="aef29-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
