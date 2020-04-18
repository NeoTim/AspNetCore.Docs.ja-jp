---
title: IIS を使用した Windows での ASP.NET Core のホスト
author: rick-anderson
description: Windows Server インターネット インフォメーション サービス (IIS) での ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 819c53f945c1e5bb2cedcef8fc39d4c8761e4549
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977029"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="d6724-103">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="d6724-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6724-104">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="d6724-105">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="d6724-106">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="d6724-106">Supported operating systems</span></span>

<span data-ttu-id="d6724-107">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="d6724-108">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-108">Windows 7 or later</span></span>
* <span data-ttu-id="d6724-109">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="d6724-110">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="d6724-111">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="d6724-112">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="d6724-113">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="d6724-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="d6724-114">Supported platforms</span></span>

<span data-ttu-id="d6724-115">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="d6724-116">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="d6724-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="d6724-117">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="d6724-118">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="d6724-119">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="d6724-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="d6724-120">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-120">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="d6724-121">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-121">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="d6724-122">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-122">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="d6724-123">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-123">In-process hosting model</span></span>

<span data-ttu-id="d6724-124">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-124">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="d6724-125">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="d6724-125">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="d6724-126">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-126">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6724-127">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="d6724-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="d6724-128">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-128">Performs app initialization.</span></span>
  * <span data-ttu-id="d6724-129">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d6724-129">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="d6724-130">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="d6724-130">Calls `Program.Main`.</span></span>
* <span data-ttu-id="d6724-131">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="d6724-131">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="d6724-132">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d6724-132">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="d6724-133">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-133">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="d6724-135">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-135">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d6724-136">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-136">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d6724-137">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-137">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="d6724-138">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-138">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="d6724-139">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-139">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6724-140">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-140">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6724-141">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-141">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="d6724-142">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="d6724-142">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="d6724-143">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-143">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="d6724-144">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="d6724-144">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="d6724-145">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-145">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

> [!NOTE]
> <span data-ttu-id="d6724-146">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="d6724-146">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d6724-147">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-147">Out-of-process hosting model</span></span>

<span data-ttu-id="d6724-148">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-148">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="d6724-149">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-149">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d6724-150">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="d6724-150">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6724-151">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-151">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="d6724-153">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="d6724-153">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d6724-154">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d6724-154">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d6724-155">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-155">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d6724-156">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-156">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="d6724-157">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-157">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d6724-158">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-158">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d6724-159">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-159">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6724-160">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-160">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6724-161">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-161">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d6724-162">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="d6724-162">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d6724-163">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-163">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6724-164">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-164">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d6724-165">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="d6724-165">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="d6724-166">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="d6724-166">Enable the IISIntegration components</span></span>

<span data-ttu-id="d6724-167">`CreateHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-167">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="d6724-168">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-168">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="d6724-169">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="d6724-169">IIS options</span></span>

<span data-ttu-id="d6724-170">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="d6724-170">**In-process hosting model**</span></span>

<span data-ttu-id="d6724-171">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="d6724-171">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="d6724-172">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-172">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="d6724-173">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-173">Option</span></span>                         | <span data-ttu-id="d6724-174">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-174">Default</span></span> | <span data-ttu-id="d6724-175">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-175">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-176">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-176">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-177">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-177">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-178">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-178">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-179">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-179">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-180">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-180">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="d6724-181">`HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるか。</span><span class="sxs-lookup"><span data-stu-id="d6724-181">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="d6724-182">`HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-182">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="d6724-183">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-183">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="d6724-184">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-184">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="d6724-185">`maxAllowedContentLength`を引き上げるには、*web.config* 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-185">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="d6724-186">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-186">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="d6724-187">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="d6724-187">**Out-of-process hosting model**</span></span>

<span data-ttu-id="d6724-188">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="d6724-188">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="d6724-189">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="d6724-189">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="d6724-190">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-190">Option</span></span>                         | <span data-ttu-id="d6724-191">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-191">Default</span></span> | <span data-ttu-id="d6724-192">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-192">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-193">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-193">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-194">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-194">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-195">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-195">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-196">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-196">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-197">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-197">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="d6724-198">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-198">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d6724-199">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d6724-199">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d6724-200">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-200">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="d6724-201">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-201">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d6724-202">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-202">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="d6724-203">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-203">web.config file</span></span>

<span data-ttu-id="d6724-204">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-204">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-205">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-205">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="d6724-206">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-206">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="d6724-207">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-207">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d6724-208">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-208">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="d6724-209">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-209">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="d6724-210">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-210">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="d6724-211">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-211">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="d6724-212">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-212">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="d6724-213">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-213">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="d6724-214">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-214">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="d6724-215">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-215">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="d6724-216">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="d6724-216">web.config file location</span></span>

<span data-ttu-id="d6724-217">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-217">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="d6724-218">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="d6724-218">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="d6724-219">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-219">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="d6724-220">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-220">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="d6724-221">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-221">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="d6724-222">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-222">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="d6724-223">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="d6724-223">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="d6724-224">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="d6724-224">Transform web.config</span></span>

<span data-ttu-id="d6724-225">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-225">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="d6724-226">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="d6724-226">IIS configuration</span></span>

<span data-ttu-id="d6724-227">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-227">**Windows Server operating systems**</span></span>

<span data-ttu-id="d6724-228">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-228">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="d6724-229">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-229">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="d6724-230">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-230">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="d6724-232">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d6724-232">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="d6724-233">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-233">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="d6724-235">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-235">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-236">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-236">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="d6724-237">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-237">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-238">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-238">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-239">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-239">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-240">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-240">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-241">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-241">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="d6724-242">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-242">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-243">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-243">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-244">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-244">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="d6724-245">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-245">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="d6724-246">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-246">**Windows desktop operating systems**</span></span>

<span data-ttu-id="d6724-247">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-247">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-248">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-248">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="d6724-249">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-249">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="d6724-250">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-250">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="d6724-251">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-251">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="d6724-252">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-252">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-253">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d6724-253">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="d6724-254">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-254">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-255">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-255">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="d6724-256">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-256">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-257">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-257">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-258">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-258">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-259">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-259">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-260">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-260">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d6724-261">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-261">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-262">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-262">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-263">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-263">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="d6724-265">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-265">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d6724-266">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-266">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="d6724-267">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-267">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-268">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d6724-268">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6724-269">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-269">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="d6724-270">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-270">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="d6724-271">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="d6724-271">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d6724-272">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-272">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="d6724-273">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="d6724-273">Direct download (current version)</span></span>

<span data-ttu-id="d6724-274">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-274">Download the installer using the following link:</span></span>

[<span data-ttu-id="d6724-275">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="d6724-275">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="d6724-276">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="d6724-276">Earlier versions of the installer</span></span>

<span data-ttu-id="d6724-277">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="d6724-277">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="d6724-278">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-278">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="d6724-279">目的の .NET Core のバージョンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-279">Click on the desired .NET Core version.</span></span>
1. <span data-ttu-id="d6724-280">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-280">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="d6724-281">**[Runtime & Hosting Bundle]\(ランタイム & ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-281">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="d6724-282">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d6724-282">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="d6724-283">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-283">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="d6724-284">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="d6724-284">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="d6724-285">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-285">Run the installer on the server.</span></span> <span data-ttu-id="d6724-286">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-286">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="d6724-287">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-287">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="d6724-288">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-288">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="d6724-289">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-289">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-290">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-290">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="d6724-291">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-291">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-292">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-292">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="d6724-293">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-293">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="d6724-294">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-294">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="d6724-295">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-295">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="d6724-296">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-296">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="d6724-297">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-297">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="d6724-298">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-298">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="d6724-299">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-299">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="d6724-300">ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。</span><span class="sxs-lookup"><span data-stu-id="d6724-300">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="d6724-301">新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-301">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="d6724-302">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-302">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="d6724-303">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-303">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="d6724-304">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-304">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="d6724-305">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="d6724-305">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="d6724-306">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-306">The preferred method is to use WebPI.</span></span> <span data-ttu-id="d6724-307">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-307">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="d6724-308">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="d6724-308">Create the IIS site</span></span>

1. <span data-ttu-id="d6724-309">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-309">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="d6724-310">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-310">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="d6724-311">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-311">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="d6724-312">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-312">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="d6724-313">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-313">Right-click the **Sites** folder.</span></span> <span data-ttu-id="d6724-314">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-314">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-315">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-315">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="d6724-316">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-316">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="d6724-318">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d6724-318">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d6724-319">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-319">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="d6724-320">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-320">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d6724-321">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-321">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="d6724-322">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d6724-322">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d6724-323">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-323">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="d6724-324">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-324">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="d6724-325">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-325">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-326">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-326">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="d6724-328">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="d6724-328">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="d6724-329">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-329">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="d6724-330">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-330">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="d6724-331">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-331">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="d6724-332">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-332">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="d6724-333">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-333">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="d6724-334">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-334">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="d6724-335">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-335">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="d6724-336">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-336">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="d6724-337">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-337">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="d6724-338">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-338">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="d6724-339">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-339">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="d6724-340">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="d6724-340">Deploy the app</span></span>

<span data-ttu-id="d6724-341">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-341">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="d6724-342">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-342">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="d6724-343">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-343">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="d6724-344">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-344">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="d6724-345">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="d6724-345">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="d6724-347">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="d6724-347">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="d6724-348">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-348">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="d6724-349">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-349">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="d6724-350">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="d6724-350">Alternatives to Web Deploy</span></span>

<span data-ttu-id="d6724-351">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-351">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="d6724-352">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-352">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="d6724-353">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="d6724-353">Browse the website</span></span>

<span data-ttu-id="d6724-354">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d6724-354">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="d6724-355">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-355">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="d6724-356">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-356">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="d6724-358">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-358">Locked deployment files</span></span>

<span data-ttu-id="d6724-359">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-359">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="d6724-360">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-360">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="d6724-361">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-361">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-362">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-362">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="d6724-363">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-363">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="d6724-364">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-364">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="d6724-365">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-365">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="d6724-366">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-366">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="d6724-367">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="d6724-367">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="d6724-368">データの保護</span><span class="sxs-lookup"><span data-stu-id="d6724-368">Data protection</span></span>

<span data-ttu-id="d6724-369">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-369">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="d6724-370">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-370">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d6724-371">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-371">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d6724-372">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-372">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d6724-373">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-373">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="d6724-374">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-374">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="d6724-375">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="d6724-375">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d6724-376">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d6724-376">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d6724-377">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-377">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-378">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-378">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="d6724-379">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-379">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="d6724-380">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-380">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="d6724-381">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-381">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="d6724-382">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-382">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="d6724-383">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-383">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="d6724-384">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-384">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="d6724-385">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-385">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="d6724-386">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-386">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="d6724-387">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-387">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="d6724-388">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-388">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="d6724-389">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-389">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="d6724-390">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-390">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="d6724-391">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="d6724-391">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="d6724-392">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-392">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="d6724-393">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-393">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="d6724-394">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-394">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="d6724-395">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-395">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="d6724-396">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="d6724-396">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="d6724-397">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-397">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="d6724-398">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-398">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="d6724-399">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-399">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="d6724-400">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-400">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="d6724-401">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-401">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="d6724-402">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-402">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="d6724-403">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="d6724-403">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="d6724-404">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="d6724-404">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="d6724-405">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-405">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="d6724-406">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-406">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="d6724-407">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-407">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="d6724-408">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-408">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="d6724-409">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-409">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="d6724-410">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-410">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="d6724-411">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="d6724-411">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="d6724-412">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="d6724-412">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="d6724-413">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-413">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="d6724-414">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d6724-414">Virtual Directories</span></span>

<span data-ttu-id="d6724-415">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d6724-415">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="d6724-416">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-416">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="d6724-417">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="d6724-417">Sub-applications</span></span>

<span data-ttu-id="d6724-418">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-418">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="d6724-419">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-419">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="d6724-420">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-420">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="d6724-421">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-421">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="d6724-422">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-422">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="d6724-423">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-423">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="d6724-424">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-424">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="d6724-425">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-425">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="d6724-426">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-426">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="d6724-427">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="d6724-427">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="d6724-428">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="d6724-428">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="d6724-429">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-429">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="d6724-430">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-430">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="d6724-431">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-431">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="d6724-432">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d6724-432">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="d6724-433">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-433">Select **OK**.</span></span>

<span data-ttu-id="d6724-434">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="d6724-434">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="d6724-435">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-435">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="d6724-436">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="d6724-436">Configuration of IIS with web.config</span></span>

<span data-ttu-id="d6724-437">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="d6724-437">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="d6724-438">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="d6724-438">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="d6724-439">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-439">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="d6724-440">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-440">For more information, see the following topics:</span></span>

* [<span data-ttu-id="d6724-441">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="d6724-441">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="d6724-442">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-442">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="d6724-443">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="d6724-443">Configuration sections of web.config</span></span>

<span data-ttu-id="d6724-444">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-444">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="d6724-445">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-445">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="d6724-446">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-446">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="d6724-447">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="d6724-447">Application Pools</span></span>

<span data-ttu-id="d6724-448">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-448">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="d6724-449">インプロセス ホスティング &ndash; 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-449">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="d6724-450">アウトプロセス ホスティング &ndash; アプリケーションをそれぞれ専用のアプリケーションプールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-450">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="d6724-451">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="d6724-451">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="d6724-452">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-452">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="d6724-453">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-453">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="d6724-454">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="d6724-454">Application Pool Identity</span></span>

<span data-ttu-id="d6724-455">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-455">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="d6724-456">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-456">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="d6724-457">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-457">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="d6724-459">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-459">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="d6724-460">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-460">Resources can be secured using this identity.</span></span> <span data-ttu-id="d6724-461">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-461">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="d6724-462">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="d6724-462">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="d6724-463">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-463">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="d6724-464">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-464">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="d6724-465">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-465">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="d6724-466">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-466">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="d6724-467">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="d6724-467">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="d6724-468">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-468">Select the **Check Names** button.</span></span> <span data-ttu-id="d6724-469">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-469">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="d6724-470">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-470">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="d6724-471">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-471">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="d6724-472">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-472">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="d6724-474">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-474">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="d6724-476">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-476">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="d6724-477">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-477">Provide additional permissions as needed.</span></span>

<span data-ttu-id="d6724-478">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-478">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="d6724-479">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-479">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="d6724-480">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-480">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d6724-481">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d6724-481">HTTP/2 support</span></span>

<span data-ttu-id="d6724-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d6724-483">インプロセス</span><span class="sxs-lookup"><span data-stu-id="d6724-483">In-process</span></span>
  * <span data-ttu-id="d6724-484">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-484">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d6724-485">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d6724-485">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d6724-486">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="d6724-486">Out-of-process</span></span>
  * <span data-ttu-id="d6724-487">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-487">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d6724-488">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-488">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d6724-489">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d6724-489">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d6724-490">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-490">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d6724-491">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-491">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d6724-492">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-492">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6724-493">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d6724-493">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d6724-494">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-494">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d6724-495">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-495">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="d6724-496">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="d6724-496">CORS preflight requests</span></span>

<span data-ttu-id="d6724-497">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-497">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="d6724-498">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-498">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="d6724-499">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-499">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="d6724-500">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d6724-500">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="d6724-501">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-501">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="d6724-502">[Application Initialization モジュール](#application-initialization-module) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-502">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="d6724-503">[アイドル タイムアウト](#idle-timeout) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-503">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="d6724-504">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="d6724-504">Application Initialization Module</span></span>

<span data-ttu-id="d6724-505">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-505">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="d6724-506">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="d6724-506">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="d6724-507">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-507">The request triggers the app to start.</span></span> <span data-ttu-id="d6724-508">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="d6724-508">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="d6724-509">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-509">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="d6724-510">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-510">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="d6724-511">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-511">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d6724-512">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-512">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="d6724-513">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-513">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d6724-514">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-514">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="d6724-515">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-515">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="d6724-516">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-516">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="d6724-517">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-517">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d6724-518">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-518">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="d6724-519">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-519">Using IIS Manager:</span></span>

  1. <span data-ttu-id="d6724-520">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-520">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d6724-521">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-521">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d6724-522">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="d6724-522">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="d6724-523">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-523">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="d6724-524">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-524">Select **OK**.</span></span>
  1. <span data-ttu-id="d6724-525">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-525">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d6724-526">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-526">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d6724-527">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="d6724-527">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="d6724-528">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-528">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="d6724-529">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-529">Select **OK**.</span></span>

* <span data-ttu-id="d6724-530">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="d6724-530">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="d6724-531">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d6724-531">Idle Timeout</span></span>

<span data-ttu-id="d6724-532">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-532">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="d6724-533">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-533">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="d6724-534">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-534">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="d6724-535">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-535">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="d6724-536">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="d6724-536">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="d6724-537">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-537">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="d6724-538">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-538">Select **OK**.</span></span>
1. <span data-ttu-id="d6724-539">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="d6724-539">Recycle the worker process.</span></span>

<span data-ttu-id="d6724-540">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="d6724-540">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="d6724-541">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="d6724-541">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="d6724-542">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="d6724-542">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="d6724-543">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d6724-543">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="d6724-544">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="d6724-544">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="d6724-545">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="d6724-545">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="d6724-546">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="d6724-546">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="d6724-547">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="d6724-547">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="d6724-548">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="d6724-548">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="d6724-549">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="d6724-549">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="d6724-550">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-550">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="d6724-551">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d6724-551">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="d6724-552">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="d6724-552">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="d6724-553">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="d6724-553">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="d6724-554">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d6724-554">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d6724-555">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-555">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="d6724-556">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-556">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="d6724-557">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="d6724-557">Supported operating systems</span></span>

<span data-ttu-id="d6724-558">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-558">The following operating systems are supported:</span></span>

* <span data-ttu-id="d6724-559">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-559">Windows 7 or later</span></span>
* <span data-ttu-id="d6724-560">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-560">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d6724-561">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-561">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="d6724-562">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-562">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="d6724-563">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-563">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="d6724-564">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-564">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="d6724-565">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="d6724-565">Supported platforms</span></span>

<span data-ttu-id="d6724-566">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-566">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="d6724-567">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="d6724-567">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="d6724-568">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-568">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="d6724-569">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-569">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="d6724-570">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="d6724-570">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="d6724-571">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-571">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="d6724-572">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-572">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="d6724-573">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-573">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="d6724-574">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-574">In-process hosting model</span></span>

<span data-ttu-id="d6724-575">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-575">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="d6724-576">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="d6724-576">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="d6724-577">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-577">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6724-578">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="d6724-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="d6724-579">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-579">Performs app initialization.</span></span>
  * <span data-ttu-id="d6724-580">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d6724-580">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="d6724-581">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="d6724-581">Calls `Program.Main`.</span></span>
* <span data-ttu-id="d6724-582">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="d6724-582">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="d6724-583">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d6724-583">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="d6724-584">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-584">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="d6724-586">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-586">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d6724-587">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-587">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d6724-588">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-588">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="d6724-589">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-589">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="d6724-590">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-590">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6724-591">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-591">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6724-592">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-592">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="d6724-593">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="d6724-593">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="d6724-594">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-594">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="d6724-595">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="d6724-595">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="d6724-596">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-596">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d6724-597">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="d6724-597">Out-of-process hosting model</span></span>

<span data-ttu-id="d6724-598">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-598">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="d6724-599">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-599">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d6724-600">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="d6724-600">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6724-601">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-601">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="d6724-603">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="d6724-603">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d6724-604">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d6724-604">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d6724-605">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-605">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d6724-606">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-606">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="d6724-607">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-607">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d6724-608">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-608">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d6724-609">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-609">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6724-610">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-610">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6724-611">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-611">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d6724-612">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="d6724-612">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d6724-613">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-613">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6724-614">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-614">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d6724-615">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="d6724-615">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="d6724-616">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="d6724-616">Enable the IISIntegration components</span></span>

<span data-ttu-id="d6724-617">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-617">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="d6724-618">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-618">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="d6724-619">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="d6724-619">IIS options</span></span>

<span data-ttu-id="d6724-620">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="d6724-620">**In-process hosting model**</span></span>

<span data-ttu-id="d6724-621">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="d6724-621">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="d6724-622">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-622">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="d6724-623">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-623">Option</span></span>                         | <span data-ttu-id="d6724-624">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-624">Default</span></span> | <span data-ttu-id="d6724-625">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-625">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-626">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-626">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-627">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-627">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-628">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-628">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-629">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-629">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-630">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-630">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="d6724-631">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="d6724-631">**Out-of-process hosting model**</span></span>

<span data-ttu-id="d6724-632">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="d6724-632">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="d6724-633">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="d6724-633">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="d6724-634">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-634">Option</span></span>                         | <span data-ttu-id="d6724-635">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-635">Default</span></span> | <span data-ttu-id="d6724-636">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-636">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-637">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-637">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-638">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-638">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-639">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-639">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-640">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-640">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-641">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-641">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="d6724-642">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-642">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d6724-643">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d6724-643">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d6724-644">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-644">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="d6724-645">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-645">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d6724-646">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-646">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="d6724-647">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-647">web.config file</span></span>

<span data-ttu-id="d6724-648">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-648">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-649">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-649">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="d6724-650">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-650">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="d6724-651">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-651">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d6724-652">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-652">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="d6724-653">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-653">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="d6724-654">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-654">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="d6724-655">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-655">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="d6724-656">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-656">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="d6724-657">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-657">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="d6724-658">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-658">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="d6724-659">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-659">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="d6724-660">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="d6724-660">web.config file location</span></span>

<span data-ttu-id="d6724-661">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-661">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="d6724-662">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="d6724-662">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="d6724-663">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-663">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="d6724-664">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-664">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="d6724-665">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-665">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="d6724-666">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-666">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="d6724-667">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="d6724-667">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="d6724-668">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="d6724-668">Transform web.config</span></span>

<span data-ttu-id="d6724-669">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-669">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="d6724-670">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="d6724-670">IIS configuration</span></span>

<span data-ttu-id="d6724-671">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-671">**Windows Server operating systems**</span></span>

<span data-ttu-id="d6724-672">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-672">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="d6724-673">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-673">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="d6724-674">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-674">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="d6724-676">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d6724-676">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="d6724-677">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-677">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="d6724-679">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-679">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-680">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-680">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="d6724-681">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-681">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-682">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-682">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-683">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-683">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-684">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-684">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-685">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-685">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="d6724-686">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-686">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-687">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-687">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-688">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-688">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="d6724-689">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-689">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="d6724-690">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-690">**Windows desktop operating systems**</span></span>

<span data-ttu-id="d6724-691">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-691">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-692">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-692">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="d6724-693">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-693">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="d6724-694">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-694">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="d6724-695">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-695">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="d6724-696">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-696">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-697">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d6724-697">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="d6724-698">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-698">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-699">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-699">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="d6724-700">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-700">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-701">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-701">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-702">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-702">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-703">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-703">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-704">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-704">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d6724-705">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-705">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-706">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-706">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-707">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-707">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="d6724-709">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-709">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d6724-710">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-710">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="d6724-711">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-711">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-712">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d6724-712">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6724-713">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-713">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="d6724-714">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-714">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="d6724-715">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="d6724-715">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d6724-716">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-716">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="d6724-717">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="d6724-717">Direct download (current version)</span></span>

<span data-ttu-id="d6724-718">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-718">Download the installer using the following link:</span></span>

[<span data-ttu-id="d6724-719">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="d6724-719">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="d6724-720">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="d6724-720">Earlier versions of the installer</span></span>

<span data-ttu-id="d6724-721">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="d6724-721">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="d6724-722">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-722">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="d6724-723">目的の .NET Core のバージョンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-723">Click on the desired .NET Core version.</span></span>
1. <span data-ttu-id="d6724-724">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-724">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="d6724-725">**[Runtime & Hosting Bundle]\(ランタイム & ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-725">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="d6724-726">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d6724-726">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="d6724-727">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-727">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="d6724-728">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="d6724-728">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="d6724-729">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-729">Run the installer on the server.</span></span> <span data-ttu-id="d6724-730">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-730">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="d6724-731">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-731">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="d6724-732">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-732">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="d6724-733">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-733">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-734">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-734">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="d6724-735">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-735">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-736">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-736">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="d6724-737">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-737">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="d6724-738">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-738">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="d6724-739">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-739">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="d6724-740">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-740">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="d6724-741">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-741">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="d6724-742">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-742">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="d6724-743">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-743">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="d6724-744">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-744">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="d6724-745">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-745">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="d6724-746">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-746">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="d6724-747">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-747">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="d6724-748">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d6724-748">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="d6724-749">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-749">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="d6724-750">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-750">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="d6724-751">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-751">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="d6724-752">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-752">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="d6724-753">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="d6724-753">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="d6724-754">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-754">The preferred method is to use WebPI.</span></span> <span data-ttu-id="d6724-755">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-755">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="d6724-756">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="d6724-756">Create the IIS site</span></span>

1. <span data-ttu-id="d6724-757">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-757">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="d6724-758">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-758">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="d6724-759">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-759">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="d6724-760">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-760">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="d6724-761">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-761">Right-click the **Sites** folder.</span></span> <span data-ttu-id="d6724-762">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-762">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-763">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-763">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="d6724-764">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-764">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="d6724-766">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d6724-766">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d6724-767">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-767">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="d6724-768">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-768">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d6724-769">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-769">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="d6724-770">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d6724-770">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d6724-771">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-771">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="d6724-772">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-772">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="d6724-773">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-773">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-774">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-774">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="d6724-776">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="d6724-776">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="d6724-777">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-777">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="d6724-778">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-778">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="d6724-779">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-779">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="d6724-780">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-780">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="d6724-781">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-781">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="d6724-782">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-782">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="d6724-783">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-783">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="d6724-784">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-784">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="d6724-785">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-785">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="d6724-786">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-786">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="d6724-787">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-787">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="d6724-788">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="d6724-788">Deploy the app</span></span>

<span data-ttu-id="d6724-789">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-789">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="d6724-790">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-790">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="d6724-791">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-791">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="d6724-792">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-792">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="d6724-793">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="d6724-793">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="d6724-795">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="d6724-795">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="d6724-796">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-796">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="d6724-797">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-797">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="d6724-798">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="d6724-798">Alternatives to Web Deploy</span></span>

<span data-ttu-id="d6724-799">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-799">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="d6724-800">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-800">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="d6724-801">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="d6724-801">Browse the website</span></span>

<span data-ttu-id="d6724-802">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d6724-802">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="d6724-803">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-803">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="d6724-804">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-804">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="d6724-806">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-806">Locked deployment files</span></span>

<span data-ttu-id="d6724-807">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-807">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="d6724-808">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-808">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="d6724-809">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-809">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-810">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-810">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="d6724-811">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-811">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="d6724-812">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-812">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="d6724-813">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-813">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="d6724-814">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-814">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="d6724-815">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="d6724-815">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="d6724-816">データの保護</span><span class="sxs-lookup"><span data-stu-id="d6724-816">Data protection</span></span>

<span data-ttu-id="d6724-817">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-817">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="d6724-818">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-818">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d6724-819">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-819">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d6724-820">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-820">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d6724-821">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-821">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="d6724-822">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-822">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="d6724-823">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="d6724-823">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d6724-824">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d6724-824">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d6724-825">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-825">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-826">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-826">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="d6724-827">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-827">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="d6724-828">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-828">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="d6724-829">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-829">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="d6724-830">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-830">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="d6724-831">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-831">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="d6724-832">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-832">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="d6724-833">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-833">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="d6724-834">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-834">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="d6724-835">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-835">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="d6724-836">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-836">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="d6724-837">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-837">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="d6724-838">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-838">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="d6724-839">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="d6724-839">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="d6724-840">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-840">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="d6724-841">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-841">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="d6724-842">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-842">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="d6724-843">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-843">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="d6724-844">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="d6724-844">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="d6724-845">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-845">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="d6724-846">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-846">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="d6724-847">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-847">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="d6724-848">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-848">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="d6724-849">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-849">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="d6724-850">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-850">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="d6724-851">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="d6724-851">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="d6724-852">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="d6724-852">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="d6724-853">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-853">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="d6724-854">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-854">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="d6724-855">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-855">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="d6724-856">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-856">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="d6724-857">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-857">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="d6724-858">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-858">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="d6724-859">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="d6724-859">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="d6724-860">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="d6724-860">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="d6724-861">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-861">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="d6724-862">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d6724-862">Virtual Directories</span></span>

<span data-ttu-id="d6724-863">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d6724-863">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="d6724-864">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-864">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="d6724-865">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="d6724-865">Sub-applications</span></span>

<span data-ttu-id="d6724-866">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-866">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="d6724-867">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-867">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="d6724-868">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-868">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="d6724-869">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-869">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="d6724-870">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-870">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="d6724-871">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-871">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="d6724-872">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-872">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="d6724-873">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-873">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="d6724-874">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-874">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="d6724-875">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="d6724-875">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="d6724-876">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="d6724-876">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="d6724-877">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-877">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="d6724-878">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-878">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="d6724-879">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-879">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="d6724-880">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d6724-880">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="d6724-881">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-881">Select **OK**.</span></span>

<span data-ttu-id="d6724-882">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="d6724-882">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="d6724-883">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-883">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="d6724-884">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="d6724-884">Configuration of IIS with web.config</span></span>

<span data-ttu-id="d6724-885">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="d6724-885">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="d6724-886">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="d6724-886">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="d6724-887">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-887">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="d6724-888">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-888">For more information, see the following topics:</span></span>

* [<span data-ttu-id="d6724-889">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="d6724-889">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="d6724-890">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-890">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="d6724-891">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="d6724-891">Configuration sections of web.config</span></span>

<span data-ttu-id="d6724-892">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-892">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="d6724-893">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-893">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="d6724-894">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-894">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="d6724-895">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="d6724-895">Application Pools</span></span>

<span data-ttu-id="d6724-896">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-896">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="d6724-897">インプロセス ホスティング &ndash; 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-897">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="d6724-898">アウトプロセス ホスティング &ndash; アプリケーションをそれぞれ専用のアプリケーションプールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-898">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="d6724-899">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="d6724-899">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="d6724-900">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-900">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="d6724-901">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-901">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="d6724-902">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="d6724-902">Application Pool Identity</span></span>

<span data-ttu-id="d6724-903">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-903">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="d6724-904">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-904">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="d6724-905">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-905">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="d6724-907">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-907">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="d6724-908">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-908">Resources can be secured using this identity.</span></span> <span data-ttu-id="d6724-909">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-909">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="d6724-910">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="d6724-910">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="d6724-911">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-911">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="d6724-912">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-912">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="d6724-913">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-913">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="d6724-914">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-914">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="d6724-915">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="d6724-915">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="d6724-916">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-916">Select the **Check Names** button.</span></span> <span data-ttu-id="d6724-917">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-917">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="d6724-918">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-918">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="d6724-919">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-919">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="d6724-920">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-920">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="d6724-922">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-922">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="d6724-924">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-924">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="d6724-925">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-925">Provide additional permissions as needed.</span></span>

<span data-ttu-id="d6724-926">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-926">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="d6724-927">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-927">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="d6724-928">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-928">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d6724-929">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d6724-929">HTTP/2 support</span></span>

<span data-ttu-id="d6724-930">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-930">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d6724-931">インプロセス</span><span class="sxs-lookup"><span data-stu-id="d6724-931">In-process</span></span>
  * <span data-ttu-id="d6724-932">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-932">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d6724-933">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d6724-933">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d6724-934">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="d6724-934">Out-of-process</span></span>
  * <span data-ttu-id="d6724-935">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-935">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d6724-936">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-936">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d6724-937">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d6724-937">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d6724-938">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-938">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d6724-939">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-939">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d6724-940">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-940">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6724-941">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d6724-941">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d6724-942">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-942">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d6724-943">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-943">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="d6724-944">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="d6724-944">CORS preflight requests</span></span>

<span data-ttu-id="d6724-945">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-945">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="d6724-946">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-946">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="d6724-947">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-947">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="d6724-948">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d6724-948">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="d6724-949">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-949">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="d6724-950">[Application Initialization モジュール](#application-initialization-module) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-950">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="d6724-951">[アイドル タイムアウト](#idle-timeout) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-951">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="d6724-952">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="d6724-952">Application Initialization Module</span></span>

<span data-ttu-id="d6724-953">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-953">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="d6724-954">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="d6724-954">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="d6724-955">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-955">The request triggers the app to start.</span></span> <span data-ttu-id="d6724-956">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="d6724-956">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="d6724-957">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-957">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="d6724-958">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-958">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="d6724-959">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-959">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d6724-960">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-960">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="d6724-961">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-961">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d6724-962">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-962">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="d6724-963">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-963">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="d6724-964">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-964">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="d6724-965">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-965">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d6724-966">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-966">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="d6724-967">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-967">Using IIS Manager:</span></span>

  1. <span data-ttu-id="d6724-968">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-968">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d6724-969">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-969">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d6724-970">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="d6724-970">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="d6724-971">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-971">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="d6724-972">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-972">Select **OK**.</span></span>
  1. <span data-ttu-id="d6724-973">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-973">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d6724-974">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-974">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d6724-975">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="d6724-975">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="d6724-976">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-976">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="d6724-977">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-977">Select **OK**.</span></span>

* <span data-ttu-id="d6724-978">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="d6724-978">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="d6724-979">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d6724-979">Idle Timeout</span></span>

<span data-ttu-id="d6724-980">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-980">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="d6724-981">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-981">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="d6724-982">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-982">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="d6724-983">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-983">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="d6724-984">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="d6724-984">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="d6724-985">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-985">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="d6724-986">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-986">Select **OK**.</span></span>
1. <span data-ttu-id="d6724-987">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="d6724-987">Recycle the worker process.</span></span>

<span data-ttu-id="d6724-988">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="d6724-988">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="d6724-989">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="d6724-989">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="d6724-990">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="d6724-990">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="d6724-991">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d6724-991">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="d6724-992">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="d6724-992">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="d6724-993">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="d6724-993">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="d6724-994">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="d6724-994">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="d6724-995">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="d6724-995">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="d6724-996">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="d6724-996">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="d6724-997">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="d6724-997">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="d6724-998">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-998">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="d6724-999">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d6724-999">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="d6724-1000">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="d6724-1000">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="d6724-1001">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="d6724-1001">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="d6724-1002">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d6724-1002">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d6724-1003">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1003">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="d6724-1004">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-1004">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="d6724-1005">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="d6724-1005">Supported operating systems</span></span>

<span data-ttu-id="d6724-1006">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1006">The following operating systems are supported:</span></span>

* <span data-ttu-id="d6724-1007">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-1007">Windows 7 or later</span></span>
* <span data-ttu-id="d6724-1008">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-1008">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d6724-1009">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1009">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="d6724-1010">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1010">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="d6724-1011">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1011">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="d6724-1012">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1012">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="d6724-1013">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="d6724-1013">Supported platforms</span></span>

<span data-ttu-id="d6724-1014">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1014">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="d6724-1015">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="d6724-1015">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="d6724-1016">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-1016">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="d6724-1017">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="d6724-1017">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="d6724-1018">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="d6724-1018">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="d6724-1019">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1019">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="d6724-1020">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1020">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="d6724-1021">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1021">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="d6724-1022">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。</span><span class="sxs-lookup"><span data-stu-id="d6724-1022">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="d6724-1023">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1023">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="d6724-1024">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1024">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d6724-1025">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="d6724-1025">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6724-1026">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1026">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="d6724-1028">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1028">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d6724-1029">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1029">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d6724-1030">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1030">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d6724-1031">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1031">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="d6724-1032">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1032">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d6724-1033">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1033">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d6724-1034">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1034">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6724-1035">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1035">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6724-1036">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1036">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d6724-1037">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1037">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d6724-1038">`CreateDefaultBuilder` は [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成し、ベース パスとポートを [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に構成することで、IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1038">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="d6724-1039">ASP.NET Core モジュールでは、バックエンド プロセスに割り当てる動的なポートが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1039">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="d6724-1040">`CreateDefaultBuilder` では <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1040">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="d6724-1041">`UseIISIntegration` では、localhost の IP アドレス (`127.0.0.1`) で動的なポートをリッスンするように Kestrel が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1041">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="d6724-1042">動的なポートが 1234 である場合、Kestrel は `127.0.0.1:1234` でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1042">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="d6724-1043">この構成によって、以下から提供されるその他の URL 構成が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1043">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="d6724-1044">Kestrel の Listen API</span><span class="sxs-lookup"><span data-stu-id="d6724-1044">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="d6724-1045">[構成](xref:fundamentals/configuration/index) (または[コマンド ラインの --urls オプション](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="d6724-1045">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="d6724-1046">モジュールを使用する場合、`UseUrls` または Kestrel の `Listen` API を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1046">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="d6724-1047">`UseUrls` または `Listen` が呼び出されると、IIS なしでアプリを実行しているときのみ、Kestrel で指定したポートがリッスンされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1047">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="d6724-1048">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1048">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6724-1049">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1049">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d6724-1050">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="d6724-1050">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="d6724-1051">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="d6724-1051">Enable the IISIntegration components</span></span>

<span data-ttu-id="d6724-1052">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1052">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="d6724-1053">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1053">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="d6724-1054">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="d6724-1054">IIS options</span></span>

| <span data-ttu-id="d6724-1055">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-1055">Option</span></span>                         | <span data-ttu-id="d6724-1056">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-1056">Default</span></span> | <span data-ttu-id="d6724-1057">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-1057">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-1058">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1058">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-1059">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1059">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-1060">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1060">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-1061">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1061">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-1062">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1062">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="d6724-1063">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1063">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="d6724-1064">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1064">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="d6724-1065">オプション</span><span class="sxs-lookup"><span data-stu-id="d6724-1065">Option</span></span>                         | <span data-ttu-id="d6724-1066">既定値</span><span class="sxs-lookup"><span data-stu-id="d6724-1066">Default</span></span> | <span data-ttu-id="d6724-1067">設定</span><span class="sxs-lookup"><span data-stu-id="d6724-1067">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6724-1068">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1068">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6724-1069">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1069">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6724-1070">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1070">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6724-1071">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1071">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6724-1072">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1072">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="d6724-1073">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1073">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d6724-1074">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d6724-1074">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d6724-1075">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1075">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="d6724-1076">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1076">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d6724-1077">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1077">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="d6724-1078">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-1078">web.config file</span></span>

<span data-ttu-id="d6724-1079">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1079">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-1080">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1080">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="d6724-1081">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1081">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="d6724-1082">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1082">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d6724-1083">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1083">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="d6724-1084">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1084">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="d6724-1085">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1085">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="d6724-1086">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1086">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="d6724-1087">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1087">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="d6724-1088">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1088">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="d6724-1089">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1089">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="d6724-1090">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1090">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="d6724-1091">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="d6724-1091">web.config file location</span></span>

<span data-ttu-id="d6724-1092">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1092">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="d6724-1093">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1093">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="d6724-1094">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1094">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="d6724-1095">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1095">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="d6724-1096">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1096">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="d6724-1097">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1097">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="d6724-1098">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="d6724-1098">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="d6724-1099">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="d6724-1099">Transform web.config</span></span>

<span data-ttu-id="d6724-1100">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1100">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="d6724-1101">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="d6724-1101">IIS configuration</span></span>

<span data-ttu-id="d6724-1102">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-1102">**Windows Server operating systems**</span></span>

<span data-ttu-id="d6724-1103">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1103">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="d6724-1104">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1104">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="d6724-1105">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1105">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="d6724-1107">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1107">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="d6724-1108">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1108">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="d6724-1110">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-1110">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-1111">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1111">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="d6724-1112">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1112">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-1113">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1113">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-1114">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-1114">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-1115">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1115">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-1116">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1116">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="d6724-1117">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1117">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-1118">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1118">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-1119">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1119">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="d6724-1120">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1120">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="d6724-1121">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d6724-1121">**Windows desktop operating systems**</span></span>

<span data-ttu-id="d6724-1122">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1122">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-1123">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1123">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="d6724-1124">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1124">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="d6724-1125">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1125">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="d6724-1126">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1126">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="d6724-1127">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1127">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d6724-1128">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1128">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="d6724-1129">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-1129">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d6724-1130">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1130">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="d6724-1131">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1131">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d6724-1132">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1132">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d6724-1133">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d6724-1133">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d6724-1134">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1134">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d6724-1135">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1135">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d6724-1136">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1136">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d6724-1137">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1137">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d6724-1138">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1138">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="d6724-1140">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-1140">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d6724-1141">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1141">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="d6724-1142">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1142">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d6724-1143">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1143">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6724-1144">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1144">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="d6724-1145">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1145">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="d6724-1146">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="d6724-1146">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d6724-1147">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1147">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="d6724-1148">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="d6724-1148">Direct download (current version)</span></span>

<span data-ttu-id="d6724-1149">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1149">Download the installer using the following link:</span></span>

[<span data-ttu-id="d6724-1150">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="d6724-1150">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="d6724-1151">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="d6724-1151">Earlier versions of the installer</span></span>

<span data-ttu-id="d6724-1152">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="d6724-1152">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="d6724-1153">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1153">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="d6724-1154">目的の .NET Core のバージョンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1154">Click on the desired .NET Core version.</span></span>
1. <span data-ttu-id="d6724-1155">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1155">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="d6724-1156">**[Runtime & Hosting Bundle]\(ランタイム & ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1156">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="d6724-1157">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1157">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="d6724-1158">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1158">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="d6724-1159">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="d6724-1159">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="d6724-1160">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1160">Run the installer on the server.</span></span> <span data-ttu-id="d6724-1161">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1161">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="d6724-1162">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1162">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="d6724-1163">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1163">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="d6724-1164">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1164">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-1165">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1165">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="d6724-1166">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1166">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d6724-1167">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1167">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="d6724-1168">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1168">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="d6724-1169">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1169">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="d6724-1170">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1170">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="d6724-1171">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-1171">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="d6724-1172">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1172">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="d6724-1173">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1173">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="d6724-1174">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1174">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="d6724-1175">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1175">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="d6724-1176">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1176">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="d6724-1177">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1177">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="d6724-1178">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1178">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="d6724-1179">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1179">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="d6724-1180">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1180">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="d6724-1181">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1181">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="d6724-1182">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="d6724-1182">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="d6724-1183">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1183">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="d6724-1184">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1184">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="d6724-1185">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1185">The preferred method is to use WebPI.</span></span> <span data-ttu-id="d6724-1186">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1186">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="d6724-1187">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="d6724-1187">Create the IIS site</span></span>

1. <span data-ttu-id="d6724-1188">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1188">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="d6724-1189">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1189">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="d6724-1190">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1190">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="d6724-1191">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1191">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="d6724-1192">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1192">Right-click the **Sites** folder.</span></span> <span data-ttu-id="d6724-1193">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1193">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-1194">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1194">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="d6724-1195">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1195">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="d6724-1197">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d6724-1197">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d6724-1198">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1198">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="d6724-1199">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1199">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d6724-1200">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1200">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="d6724-1201">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d6724-1201">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d6724-1202">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1202">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="d6724-1203">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1203">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="d6724-1204">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1204">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="d6724-1205">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1205">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="d6724-1207">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1207">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="d6724-1208">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1208">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="d6724-1209">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1209">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="d6724-1210">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1210">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="d6724-1211">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1211">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="d6724-1212">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1212">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="d6724-1213">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1213">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="d6724-1214">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1214">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="d6724-1215">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1215">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="d6724-1216">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1216">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="d6724-1217">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d6724-1217">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="d6724-1218">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1218">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="d6724-1219">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="d6724-1219">Deploy the app</span></span>

<span data-ttu-id="d6724-1220">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1220">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="d6724-1221">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1221">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="d6724-1222">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-1222">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="d6724-1223">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1223">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="d6724-1224">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1224">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="d6724-1226">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="d6724-1226">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="d6724-1227">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1227">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="d6724-1228">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1228">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="d6724-1229">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="d6724-1229">Alternatives to Web Deploy</span></span>

<span data-ttu-id="d6724-1230">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1230">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="d6724-1231">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1231">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="d6724-1232">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="d6724-1232">Browse the website</span></span>

<span data-ttu-id="d6724-1233">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d6724-1233">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="d6724-1234">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1234">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="d6724-1235">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1235">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="d6724-1237">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="d6724-1237">Locked deployment files</span></span>

<span data-ttu-id="d6724-1238">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1238">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="d6724-1239">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1239">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="d6724-1240">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1240">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-1241">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1241">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="d6724-1242">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1242">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="d6724-1243">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1243">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="d6724-1244">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1244">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="d6724-1245">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1245">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="d6724-1246">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="d6724-1246">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="d6724-1247">データの保護</span><span class="sxs-lookup"><span data-stu-id="d6724-1247">Data protection</span></span>

<span data-ttu-id="d6724-1248">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1248">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="d6724-1249">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1249">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d6724-1250">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1250">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d6724-1251">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1251">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d6724-1252">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1252">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="d6724-1253">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1253">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="d6724-1254">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1254">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d6724-1255">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1255">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d6724-1256">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1256">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="d6724-1257">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-1257">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="d6724-1258">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1258">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="d6724-1259">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1259">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="d6724-1260">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1260">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="d6724-1261">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1261">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="d6724-1262">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1262">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="d6724-1263">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1263">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="d6724-1264">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1264">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="d6724-1265">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1265">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="d6724-1266">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1266">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="d6724-1267">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1267">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="d6724-1268">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1268">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="d6724-1269">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="d6724-1269">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="d6724-1270">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1270">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="d6724-1271">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1271">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="d6724-1272">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1272">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="d6724-1273">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1273">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="d6724-1274">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1274">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="d6724-1275">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1275">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="d6724-1276">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1276">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="d6724-1277">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-1277">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="d6724-1278">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1278">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="d6724-1279">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1279">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="d6724-1280">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1280">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="d6724-1281">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1281">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="d6724-1282">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="d6724-1282">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="d6724-1283">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1283">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="d6724-1284">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1284">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="d6724-1285">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1285">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="d6724-1286">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="d6724-1286">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="d6724-1287">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1287">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="d6724-1288">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1288">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="d6724-1289">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1289">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="d6724-1290">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="d6724-1290">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="d6724-1291">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1291">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="d6724-1292">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1292">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="d6724-1293">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d6724-1293">Virtual Directories</span></span>

<span data-ttu-id="d6724-1294">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1294">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="d6724-1295">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1295">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="d6724-1296">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="d6724-1296">Sub-applications</span></span>

<span data-ttu-id="d6724-1297">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1297">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="d6724-1298">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1298">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="d6724-1299">サブアプリに、ハンドラーとして ASP.NET Core モジュールを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1299">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="d6724-1300">このモジュールをサブアプリの *web.config* ファイルにハンドラーとして追加すると、サブアプリを閲覧しようとすると、エラーのある構成ファイルを参照する *500.19 内部サーバー エラー* が返されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1300">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="d6724-1301">次の例は、ASP.NET Core サブアプリの発行済み *web.config* ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1301">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="d6724-1302">ASP.NET Core アプリの下に ASP.NET Core 以外のサブアプリをホスティングする場合は、サブアプリの *web.config* ファイルにある継承されたハンドラーを明示的に削除します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1302">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="d6724-1303">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1303">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="d6724-1304">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1304">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="d6724-1305">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1305">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="d6724-1306">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1306">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="d6724-1307">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1307">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="d6724-1308">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1308">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="d6724-1309">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1309">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="d6724-1310">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="d6724-1310">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="d6724-1311">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1311">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="d6724-1312">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1312">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="d6724-1313">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1313">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="d6724-1314">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1314">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="d6724-1315">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1315">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="d6724-1316">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1316">Select **OK**.</span></span>

<span data-ttu-id="d6724-1317">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1317">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="d6724-1318">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1318">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="d6724-1319">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="d6724-1319">Configuration of IIS with web.config</span></span>

<span data-ttu-id="d6724-1320">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1320">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="d6724-1321">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1321">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="d6724-1322">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1322">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="d6724-1323">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1323">For more information, see the following topics:</span></span>

* [<span data-ttu-id="d6724-1324">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="d6724-1324">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="d6724-1325">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1325">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="d6724-1326">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="d6724-1326">Configuration sections of web.config</span></span>

<span data-ttu-id="d6724-1327">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1327">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="d6724-1328">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1328">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="d6724-1329">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1329">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="d6724-1330">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="d6724-1330">Application Pools</span></span>

<span data-ttu-id="d6724-1331">サーバーで複数の Web サイトをホストする場合は、アプリをそれぞれ専用のアプリ プールで実行して、アプリを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1331">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="d6724-1332">IIS の **[Web サイトの追加]** ダイアログはこの構成の既定です。</span><span class="sxs-lookup"><span data-stu-id="d6724-1332">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="d6724-1333">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1333">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="d6724-1334">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1334">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="d6724-1335">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="d6724-1335">Application Pool Identity</span></span>

<span data-ttu-id="d6724-1336">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1336">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="d6724-1337">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1337">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="d6724-1338">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1338">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="d6724-1340">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1340">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="d6724-1341">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1341">Resources can be secured using this identity.</span></span> <span data-ttu-id="d6724-1342">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1342">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="d6724-1343">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1343">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="d6724-1344">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1344">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="d6724-1345">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1345">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="d6724-1346">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1346">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="d6724-1347">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1347">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="d6724-1348">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1348">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="d6724-1349">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1349">Select the **Check Names** button.</span></span> <span data-ttu-id="d6724-1350">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1350">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="d6724-1351">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1351">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="d6724-1352">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1352">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="d6724-1353">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1353">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="d6724-1355">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1355">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="d6724-1357">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6724-1357">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="d6724-1358">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1358">Provide additional permissions as needed.</span></span>

<span data-ttu-id="d6724-1359">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="d6724-1359">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="d6724-1360">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1360">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="d6724-1361">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1361">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d6724-1362">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d6724-1362">HTTP/2 support</span></span>

<span data-ttu-id="d6724-1363">次の基本要件を満たすアウトプロセスの展開には、[HTTP/2](https://httpwg.org/specs/rfc7540.html) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1363">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="d6724-1364">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d6724-1364">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="d6724-1365">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1365">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="d6724-1366">ターゲット フレームワーク:HTTP/2 接続は IIS によって完全に処理されるため、アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1366">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="d6724-1367">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d6724-1367">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d6724-1368">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d6724-1368">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d6724-1369">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d6724-1369">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d6724-1370">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d6724-1370">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d6724-1371">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1371">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="d6724-1372">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="d6724-1372">CORS preflight requests</span></span>

<span data-ttu-id="d6724-1373">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d6724-1373">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="d6724-1374">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="d6724-1374">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="d6724-1375">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d6724-1375">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="d6724-1376">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="d6724-1376">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="d6724-1377">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="d6724-1377">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="d6724-1378">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="d6724-1378">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="d6724-1379">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="d6724-1379">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="d6724-1380">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d6724-1380">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="d6724-1381">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="d6724-1381">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="d6724-1382">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="d6724-1382">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="d6724-1383">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d6724-1383">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
