---
title: Nginx 搭載の Linux で ASP.NET Core をホストする
author: rick-anderson
description: Ubuntu 16.04 でリバース プロキシとして Nginx をセットアップし、Kestrel で実行している ASP.NET Core Web アプリに HTTP トラフィックを転送する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: ceb2ad857649dcfa8d04420dcc37792495edc3ff
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224025"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="48ba4-103">Nginx 搭載の Linux で ASP.NET Core をホストする</span><span class="sxs-lookup"><span data-stu-id="48ba4-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="48ba4-104">[Sourabh Shirhatti](https://twitter.com/sshirhatti) による投稿</span><span class="sxs-lookup"><span data-stu-id="48ba4-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="48ba4-105">このガイドでは、Ubuntu 16.04 サーバーで本稼働対応の ASP.NET Core 環境をセットアップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="48ba4-106">これらの手順は、Ubuntu のより新しいバージョンで動作する可能性が高いですが、手順はまだ新しいバージョンでテストされていません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="48ba4-107">ASP.NET Core でサポートされている他の Linux ディストリビューションについて詳しくは、「[Linux における .NET Core の前提条件](/dotnet/core/linux-prerequisites)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="48ba4-108">Ubuntu 14.04 の場合、Kestrel プロセスを監視するためのソリューションとして *supervisord* が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="48ba4-109">*systemd* は Ubuntu 14.04 ではご利用いただけません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="48ba4-110">Ubuntu 14.04 の手順については、[このトピックの前のバージョンを参照してください](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="48ba4-111">このガイドでは:</span><span class="sxs-lookup"><span data-stu-id="48ba4-111">This guide:</span></span>

* <span data-ttu-id="48ba4-112">リバース プロキシ サーバーの背後に既存の ASP.NET Core アプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="48ba4-113">Kestrel Web サーバーに要求を転送するようにリバース プロキシ サーバーを設定します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="48ba4-114">Web アプリを起動時にデーモンとして実行します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="48ba4-115">Web アプリの再起動に役立つようにプロセス管理ツールを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="48ba4-116">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="48ba4-116">Prerequisites</span></span>

1. <span data-ttu-id="48ba4-117">Ubuntu 16.04 サーバーへのアクセスと sudo 特権が与えられた標準ユーザー アカウント。</span><span class="sxs-lookup"><span data-stu-id="48ba4-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="48ba4-118">サーバーへの .NET Core ランタイムのインストール。</span><span class="sxs-lookup"><span data-stu-id="48ba4-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="48ba4-119">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="48ba4-120">プレビューでない最新の .NET Core バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="48ba4-121">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** の下の表でプレビューでない最新のランタイムをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="48ba4-122">Linux の 「**パッケージ マネージャーの手順**」 リンクを選択し、ご利用の Ubuntu バージョン用の Ubuntu 手順に従います。</span><span class="sxs-lookup"><span data-stu-id="48ba4-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="48ba4-123">既存の ASP.NET Core アプリ。</span><span class="sxs-lookup"><span data-stu-id="48ba4-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="48ba4-124">共有フレームワークをアップグレードした後の任意の時点で、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="48ba4-125">アプリを介して発行およびコピーする</span><span class="sxs-lookup"><span data-stu-id="48ba4-125">Publish and copy over the app</span></span>

<span data-ttu-id="48ba4-126">[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="48ba4-127">アプリがローカル環境で実行されていて、セキュリティで保護された接続 (HTTPS) を行うように構成されていない場合は、次の方法のいずれかを採用します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="48ba4-128">セキュリティで保護されたローカル接続を処理するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="48ba4-129">詳しくは、「[HTTPS の構成](#https-configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="48ba4-130">*Properties/launchSettings.json* ファイルの `applicationUrl` プロパティから `https://localhost:5001` を削除します (ある場合)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="48ba4-131">開発環境から [dotnet publish](/dotnet/core/tools/dotnet-publish) を実行し、サーバー上で実行できるディレクトリ (たとえば、*bin/Release/&lt;target_framework_moniker&gt;/publish*) にアプリをパッケージします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="48ba4-132">サーバーで .NET Core ランタイムを管理しない場合、アプリは[独立した展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行することもできます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="48ba4-133">組織のワークフローに統合されているツール (SCP や SFTP など) を使用して、サーバーに ASP.NET Core アプリをコピーします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="48ba4-134">Web アプリは一般的に *var* ディレクトリの下に配置されます (たとえば、*var/www/helloapp*)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="48ba4-135">運用展開シナリオの場合、継続的インテグレーション ワークフローが、アプリの発行処理とサーバーへの資産のコピーを行います。</span><span class="sxs-lookup"><span data-stu-id="48ba4-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="48ba4-136">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-136">Test the app:</span></span>

1. <span data-ttu-id="48ba4-137">コマンド ラインから `dotnet <app_assembly>.dll` アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="48ba4-138">ブラウザーで、`http://<serveraddress>:<port>` に移動し、アプリが Linux のローカルで動作することを検証します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="48ba4-139">リバース プロキシ サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="48ba4-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="48ba4-140">リバース プロキシは、動的 Web アプリを提供するための一般的な仕組みです。</span><span class="sxs-lookup"><span data-stu-id="48ba4-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="48ba4-141">リバース プロキシは HTTP 要求を終了させ、ASP.NET Core アプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="48ba4-142">リバース プロキシ サーバーを利用する</span><span class="sxs-lookup"><span data-stu-id="48ba4-142">Use a reverse proxy server</span></span>

<span data-ttu-id="48ba4-143">Kestrel は、ASP.NET Core から動的なコンテンツを提供するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="48ba4-144">ただし、Web サーバーとしての機能は、IIS、Apache、Nginx などのサーバーと比べると制限されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="48ba4-145">リバース プロキシ サーバーは、静的コンテンツ サービス、要求のキャッシュ、要求の圧縮、HTTP サーバーからの HTTPS 終了などの作業の負荷を軽減します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="48ba4-146">リバース プロキシ サーバーは専用コンピューター上に置かれることもあれば、HTTP サーバーと並んで展開されることもあります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="48ba4-147">このガイドの目的のために、単一インスタンスの Nginx が使用されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="48ba4-148">HTTP サーバーと並んで、同じサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="48ba4-149">要件に応じて、別のセットアップを選択することも可能です。</span><span class="sxs-lookup"><span data-stu-id="48ba4-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="48ba4-150">要求はリバース プロキシによって転送されます。そのため、[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) を使用します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="48ba4-151">リダイレクト URI とその他のセキュリティ ポリシーを正しく機能させるために、このミドルウェアは、`X-Forwarded-Proto` ヘッダーを利用して、`Request.Scheme` を更新します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="48ba4-152">認証、リンクの生成、リダイレクト、および地理的位置情報など、スキームに依存するすべてのコンポーネントは、Forwarded Headers Middleware の呼び出し後に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="48ba4-153">一般的な規則として、Forwarded Headers Middleware は、診断およびエラー処理ミドルウェアを除くその他のミドルウェアより前に実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="48ba4-154">この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="48ba4-155">他のミドルウェアを呼び出す前に、`Startup.Configure` の一番上にある <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="48ba4-156">ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="48ba4-157">ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合、転送される既定のヘッダーは `None` です。</span><span class="sxs-lookup"><span data-stu-id="48ba4-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="48ba4-158">標準 localhost アドレス (127.0.0.1) など、ループバック アドレス (127.0.0.0/8、[::1]) 上で実行するプロキシは、既定で信頼されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="48ba4-159">組織内のその他の信頼されているプロキシまたはネットワークによってインターネットと Web サーバーの間の要求が処理される場合は、それらを、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> を使用して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> のリストに追加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="48ba4-160">次の例では、IP アドレス 10.0.0.100 にある信頼されているプロキシ サーバーが `Startup.ConfigureServices` 内の Forwarded Headers Middleware `KnownProxies` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="48ba4-161">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="48ba4-162">Nginx をインストールする</span><span class="sxs-lookup"><span data-stu-id="48ba4-162">Install Nginx</span></span>

<span data-ttu-id="48ba4-163">`apt-get` を利用し、Nginx をインストールします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="48ba4-164">インストーラーにより *systemd* init スクリプトが作成されます。このスクリプトがシステム起動時に Nginx をデーモンとして実行します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="48ba4-165">「[Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)」 (Nginx: 公式 Debian/Ubuntu パッケージ) にある Ubuntu 用のインストールの指示に従います。</span><span class="sxs-lookup"><span data-stu-id="48ba4-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="48ba4-166">オプションの Nginx モジュールが必要な場合、Nginx をソースからビルドする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="48ba4-167">Nginx は初めてのインストールとなるので、次を実行して明示的に起動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="48ba4-168">ブラウザーで Nginx の既定のランディング ページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="48ba4-169">ランディング ページは `http://<server_IP_address>/index.nginx-debian.html` からアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="48ba4-170">Nginx を構成する</span><span class="sxs-lookup"><span data-stu-id="48ba4-170">Configure Nginx</span></span>

<span data-ttu-id="48ba4-171">Nginx をリバース プロキシとして構成し、ASP.NET Core アプリに要求を転送するには、 */etc/nginx/sites-available/default* を変更します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="48ba4-172">テキスト エディターで開き、中身を次のものに変更します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-172">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="48ba4-173">SignalR Websocket に依存する Blazor サーバー アプリの場合、`Connection` ヘッダーを設定する方法については、「<xref:host-and-deploy/blazor/server#linux-with-nginx>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="48ba4-174">`server_name` が一致しない場合、Nginx では既定のサーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="48ba4-175">既定のサーバーが定義されていない場合、構成ファイルの最初のサーバーが既定のサーバーとなります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="48ba4-176">ベスト プラクティスとして、構成ファイルで 444 の状態コードを返す既定のサーバーを具体的に追加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="48ba4-177">既定のサーバーの構成例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="48ba4-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="48ba4-178">上記の構成ファイルと既定のサーバーでは、Nginx は、ホスト ヘッダー `example.com` または `*.example.com` で、ポート 80 でパブリック トラフィックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="48ba4-179">これらのホストと一致しない要求は、Kestrel に転送されません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="48ba4-180">Nginx は一致する要求を Kestrel (`http://localhost:5000`) に転送します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="48ba4-181">詳細については、「[How nginx processes a request](https://nginx.org/docs/http/request_processing.html)」(Nginx が要求を処理する方法) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="48ba4-182">Kestrel の IP/ポートを変更するには、[Kestrel のエンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="48ba4-183">適切な [server_name directive](https://nginx.org/docs/http/server_names.html) を指定しないと、アプリにセキュリティ上の脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="48ba4-184">親ドメイン全体を制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.example.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="48ba4-185">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="48ba4-186">Nginx の構成を確立したら、`sudo nginx -t` を実行して構成ファイルの構文を確認します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="48ba4-187">構成ファイルがテストに合格したら、`sudo nginx -s reload` を実行することで、強制的に Nginx に変更を反映させます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="48ba4-188">アプリをサーバーで直接実行するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="48ba4-189">アプリのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="48ba4-190">アプリを実行する:`dotnet <app_assembly.dll>`。ここで、`app_assembly.dll` はアプリのアセンブリ ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="48ba4-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="48ba4-191">アプリがサーバーで実行されているにも関わらず、インターネット上で応答がない場合、サーバーのファイアウォールを確認し、ポート 80 が開かれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="48ba4-192">Azure Ubuntu VM を使用している場合、受信ポート 80 のトラフィックを有効にするネットワーク セキュリティ グループ (NSG) 規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="48ba4-193">送信トラフィックは、受信規則が有効になると自動的に生成されるので、送信ポート 80 規則を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="48ba4-194">アプリのテストが終了したら、コマンド プロンプトで `Ctrl+C` を使用してアプリをシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="48ba4-195">アプリを監視する</span><span class="sxs-lookup"><span data-stu-id="48ba4-195">Monitor the app</span></span>

<span data-ttu-id="48ba4-196">サーバーは、`http://<serveraddress>:80` に対する要求を Kestrel で実行されている ASP.NET Core アプリ (`http://127.0.0.1:5000`) に転送するようにセットアップされました。</span><span class="sxs-lookup"><span data-stu-id="48ba4-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="48ba4-197">ただし、Nginx は Kestrel プロセスを管理するようには設定されていません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="48ba4-198">*systemd* を使用してサービス ファイルを作成し、基になる Web アプリを起動して監視できます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="48ba4-199">*systemd* は init システムであり、プロセスを起動、停止、管理するためのさまざまな高性能機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="48ba4-200">サービス ファイルを作成する</span><span class="sxs-lookup"><span data-stu-id="48ba4-200">Create the service file</span></span>

<span data-ttu-id="48ba4-201">次のように、サービス定義ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="48ba4-202">アプリのサービス ファイルの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-202">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="48ba4-203">前の例では、サービスを管理するユーザーは `User` オプションによって指定されています。</span><span class="sxs-lookup"><span data-stu-id="48ba4-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="48ba4-204">ユーザー (`www-data`) が存在し、アプリのファイルの適切な所有権を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="48ba4-205">アプリが最初の割り込み信号を受信してからシャットダウンするのを待機する期間を構成するには、`TimeoutStopSec` を使用します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="48ba4-206">この期間内にアプリがシャットダウンしない場合は、SIGKILL を発行してアプリを終了します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="48ba4-207">タイムアウトを無効にするには、値として、単位なしの秒数 (`150` など)、期間の値 (`2min 30s` など)、または `infinity` を指定します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="48ba4-208">`TimeoutStopSec` は、既定ではマネージャー構成ファイル (*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*) 内の `DefaultTimeoutStopSec` の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="48ba4-209">ほとんどのディストリビューションにおいて、タイムアウトの既定値は 90 秒となります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="48ba4-210">Linux のファイル システムは大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="48ba4-211">ASPNETCORE_ENVIRONMENT を "Production" に設定すると、構成ファイル *appsettings.Production.json* が検索されます。*appsettings.production.json* ではありません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="48ba4-212">構成プロバイダーが環境変数を読み取れるようにするために、一部の値 (たとえば SQL の接続文字列) をエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="48ba4-213">次のコマンドを使用して、構成ファイルで使用するために適切にエスケープされた値を生成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="48ba4-214">コロン (`:`) 区切り記号は、環境変数の名前ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="48ba4-215">コロンの代わりに 2 つのアンダースコア (`__`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="48ba4-216">環境変数が構成に読み取られるときに、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)によって 2 つのアンダースコアがコロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="48ba4-217">次の例では、接続文字列キー `ConnectionStrings:DefaultConnection` はサービス定義ファイルでは `ConnectionStrings__DefaultConnection` と設定されています。</span><span class="sxs-lookup"><span data-stu-id="48ba4-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="48ba4-218">ファイルを保存し、サービスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="48ba4-219">サービスを起動し、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-219">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="48ba4-220">リバース プロキシが構成され、Kestrel は systemd 経由で管理されます。これで Web アプリは完全に構成され、`http://localhost` でローカル コンピューター上のブラウザーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="48ba4-221">妨げとなるファイアウォールがなければ、リモート コンピューターからもアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="48ba4-222">応答ヘッダーを調べると、ASP.NET Core アプリが Kestrel によってサービス提供されていることが `Server` ヘッダーに示されています。</span><span class="sxs-lookup"><span data-stu-id="48ba4-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="48ba4-223">ログを表示する</span><span class="sxs-lookup"><span data-stu-id="48ba4-223">View logs</span></span>

<span data-ttu-id="48ba4-224">Kestrel を利用する Web アプリは `systemd` を使用して管理されるため、すべてのイベントとプロセスが記録され、中心的ジャーナルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="48ba4-225">ただし、このジャーナルには、`systemd` が管理するすべてのサービスとプロセスのすべてのエントリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="48ba4-226">`kestrel-helloapp.service` 固有の項目を表示するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="48ba4-227">さらに絞り込むには、時間オプションとして `--since today`、`--until 1 hour ago`、あるいはこの 2 つの組み合わせを利用すれば、返されるエントリの数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="48ba4-228">データの保護</span><span class="sxs-lookup"><span data-stu-id="48ba4-228">Data protection</span></span>

<span data-ttu-id="48ba4-229">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証ミドルウェア (Cookie ミドルウェアなど) やクロスサイト リクエスト フォージェリ (CSRF) 保護を含む、いくつかの ASP.NET Core [ ミドルウェア](xref:fundamentals/middleware/index)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="48ba4-230">データ保護 API がユーザーのコードから呼び出されない場合でも、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="48ba4-231">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="48ba4-232">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="48ba4-233">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="48ba4-234">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="48ba4-235">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="48ba4-236">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="48ba4-237">キー リングを永続化して暗号化するようにデータ保護を構成する場合は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="48ba4-238">要求ヘッダー フィールドが長すぎます</span><span class="sxs-lookup"><span data-stu-id="48ba4-238">Long request header fields</span></span>

<span data-ttu-id="48ba4-239">プロキシ サーバーの既定の設定では、通常、プラットフォームに応じて、要求ヘッダー フィールドが 4 K または 8 K に制限されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="48ba4-240">アプリでは、既定値よりも長いフィールドが必要になる場合があります (たとえば、[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用するアプリ)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="48ba4-241">長いフィールドが必要な場合は、プロキシ サーバーの既定の設定に対して調整が必要になります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="48ba4-242">適用する値は、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="48ba4-243">詳細については、ご利用のサーバーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="48ba4-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="48ba4-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="48ba4-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="48ba4-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="48ba4-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="48ba4-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="48ba4-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="48ba4-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="48ba4-248">必要な場合を除いて、プロキシ バッファーの既定値を増やさないでください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="48ba4-249">これらの値を増やすと、悪意のあるユーザーによるバッファー オーバーラン (オーバーフロー) とサービス拒否 (DoS) の攻撃のリスクが増加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="48ba4-250">アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="48ba4-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="48ba4-251">AppArmor を有効にする</span><span class="sxs-lookup"><span data-stu-id="48ba4-251">Enable AppArmor</span></span>

<span data-ttu-id="48ba4-252">Linux Security Modules (LSM) は、Linux 2.6 以降の Linux カーネルに含まれるフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="48ba4-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="48ba4-253">LSM は、セキュリティ モジュールのさまざまな実装に対応しています。</span><span class="sxs-lookup"><span data-stu-id="48ba4-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="48ba4-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) は Mandatory Access Control システムを実装する LSM です。このシステムは、プログラムのリソース範囲を限定できます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="48ba4-255">AppArmor が有効であり、正しく構成されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="48ba4-256">ファイアウォールを構成する</span><span class="sxs-lookup"><span data-stu-id="48ba4-256">Configure the firewall</span></span>

<span data-ttu-id="48ba4-257">使用されていないすべての外部ポートを閉じます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="48ba4-258">Uncomplicated firewall (ufw) は `iptables` のフロント エンドとなり、ファイアウォールを構成するための CLI が提供されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="48ba4-259">ファイアウォールが正しく構成されていない場合、システム全体にアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="48ba4-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="48ba4-260">正しい SSH ポートを指定しないと、SSH を使用してシステムに接続する場合に、そのシステムから事実上閉め出されることになります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="48ba4-261">既定のポートは 22 です。</span><span class="sxs-lookup"><span data-stu-id="48ba4-261">The default port is 22.</span></span> <span data-ttu-id="48ba4-262">詳細については、[ufw の概要](https://help.ubuntu.com/community/UFW)と[マニュアル](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="48ba4-263">`ufw` をインストールし、必要なすべてのポートでトラフィックを許可するように構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="48ba4-264">Nginx をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="48ba4-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="48ba4-265">Nginx 応答名を変更する</span><span class="sxs-lookup"><span data-stu-id="48ba4-265">Change the Nginx response name</span></span>

<span data-ttu-id="48ba4-266">*src/http/ngx_http_header_filter_module.c* を編集します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="48ba4-267">構成オプション</span><span class="sxs-lookup"><span data-stu-id="48ba4-267">Configure options</span></span>

<span data-ttu-id="48ba4-268">必要なその他のモジュールでサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="48ba4-269">アプリのセキュリティを強化するために、[ModSecurity](https://www.modsecurity.org/) のような Web アプリのファイアウォールの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="48ba4-270">HTTPS の構成</span><span class="sxs-lookup"><span data-stu-id="48ba4-270">HTTPS configuration</span></span>

<span data-ttu-id="48ba4-271">**セキュリティで保護された (HTTPS) ローカル接続用にアプリを構成する**</span><span class="sxs-lookup"><span data-stu-id="48ba4-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="48ba4-272">[dotnet run](/dotnet/core/tools/dotnet-run) コマンドでは、アプリの *Properties/launchSettings.json* ファイルが使用されます。このファイルでは、`applicationUrl` プロパティによって提供される URL でリッスンするように、アプリが構成されます (例: `https://localhost:5001;http://localhost:5000`)。</span><span class="sxs-lookup"><span data-stu-id="48ba4-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="48ba4-273">次のいずれかの方法を使用して、`dotnet run` コマンド用の開発または開発環境 (Visual Studio Code の F5 または Ctrl + F5 キー) で証明書を使用するように、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="48ba4-274">[構成から既定の証明書を置き換える](xref:fundamentals/servers/kestrel#configuration) (*推奨*)</span><span class="sxs-lookup"><span data-stu-id="48ba4-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="48ba4-275">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="48ba4-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="48ba4-276">**セキュリティで保護された (HTTPS) クライアント接続用にリバース プロキシを構成する**</span><span class="sxs-lookup"><span data-stu-id="48ba4-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="48ba4-277">信頼できる証明機関 (CA) が発行した、有効な証明書を指定することで、ポート `443` で HTTPS トラフィックを待ち受けるようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="48ba4-278">次の */etc/nginx/nginx.conf* ファイルで示されているプラクティスの一部を採用することで、セキュリティを強化します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="48ba4-279">たとえば、強力な暗号を選択したり、HTTP 経由のすべてのトラフィックを HTTPS にリダイレクトしたりします。</span><span class="sxs-lookup"><span data-stu-id="48ba4-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="48ba4-280">`HTTP Strict-Transport-Security` (HSTS) ヘッダーを追加すると、クライアントが行う後続のすべての要求が HTTPS 経由になります。</span><span class="sxs-lookup"><span data-stu-id="48ba4-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="48ba4-281">HSTS ヘッダーを追加しないでください。今後 HTTPS を利用できないことがあれば、適切な `max-age` を選択してください。</span><span class="sxs-lookup"><span data-stu-id="48ba4-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="48ba4-282">*/etc/nginx/proxy.conf* 構成ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="48ba4-283">*/etc/nginx/nginx.conf* 構成ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="48ba4-284">この例では、1 つの構成ファイルに `http` セクションと `server` セクションの両方が含まれています。</span><span class="sxs-lookup"><span data-stu-id="48ba4-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="48ba4-285">Nginx をクリックジャッキングから守る</span><span class="sxs-lookup"><span data-stu-id="48ba4-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="48ba4-286">[クリックジャッキング](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)は "*UI 着せ替え攻撃*" とも呼ばれ、Web サイトの訪問者を騙して現在訪れているものとは異なるページのリンクやボタンをクリックさせる悪意のある攻撃です。</span><span class="sxs-lookup"><span data-stu-id="48ba4-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="48ba4-287">サイトをセキュリティで保護するには、`X-FRAME-OPTIONS` を使います。</span><span class="sxs-lookup"><span data-stu-id="48ba4-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="48ba4-288">クリックジャッキング攻撃を軽減するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="48ba4-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="48ba4-289">*nginx.conf* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="48ba4-290">行 `add_header X-Frame-Options "SAMEORIGIN";` を追加します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="48ba4-291">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-291">Save the file.</span></span>
1. <span data-ttu-id="48ba4-292">Nginx を再起動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="48ba4-293">MIME タイプ スニッフィング</span><span class="sxs-lookup"><span data-stu-id="48ba4-293">MIME-type sniffing</span></span>

<span data-ttu-id="48ba4-294">このヘッダーは応答コンテンツの種類をオーバーライドしないようにブラウザーに指示するので、ほとんどのブラウザーで MIME スニッフィングが阻止されます。</span><span class="sxs-lookup"><span data-stu-id="48ba4-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="48ba4-295">`nosniff` オプションを指定すると、サーバーがコンテンツを "text/html" と読むとき、ブラウザーはそれを "text/html" として表示します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="48ba4-296">*nginx.conf* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="48ba4-297">行 `add_header X-Content-Type-Options "nosniff";` を追加し、ファイルを保存し、Nginx を再起動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="48ba4-298">Nginx に関するその他の推奨事項</span><span class="sxs-lookup"><span data-stu-id="48ba4-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="48ba4-299">サーバー上で共有フレームワークをアップグレードしたら、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="48ba4-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48ba4-300">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="48ba4-300">Additional resources</span></span>

* [<span data-ttu-id="48ba4-301">Linux における .NET Core の前提条件</span><span class="sxs-lookup"><span data-stu-id="48ba4-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="48ba4-302">Nginx: バイナリ リリース: 公式 Debian/Ubuntu パッケージ</span><span class="sxs-lookup"><span data-stu-id="48ba4-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="48ba4-303">NGINX: 転送されるヘッダーの使用</span><span class="sxs-lookup"><span data-stu-id="48ba4-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
