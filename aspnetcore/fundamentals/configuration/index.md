---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: df49286c3f050b8e90cb5427cf03e2b896a39294
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885563"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="901b1-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="901b1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="901b1-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="901b1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="901b1-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="901b1-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="901b1-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="901b1-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="901b1-107">Azure Key Vault</span></span>
* <span data-ttu-id="901b1-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="901b1-108">Azure App Configuration</span></span>
* <span data-ttu-id="901b1-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="901b1-109">Command-line arguments</span></span>
* <span data-ttu-id="901b1-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="901b1-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="901b1-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="901b1-111">Directory files</span></span>
* <span data-ttu-id="901b1-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="901b1-112">Environment variables</span></span>
* <span data-ttu-id="901b1-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="901b1-113">In-memory .NET objects</span></span>
* <span data-ttu-id="901b1-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="901b1-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="901b1-115">一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="901b1-116">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="901b1-117">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="901b1-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="901b1-118">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="901b1-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="901b1-119">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="901b1-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="901b1-120">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="901b1-121">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="901b1-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="901b1-122">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="901b1-122">Host versus app configuration</span></span>

<span data-ttu-id="901b1-123">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="901b1-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="901b1-124">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="901b1-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="901b1-125">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="901b1-126">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="901b1-127">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="901b1-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="901b1-128">その他の構成</span><span class="sxs-lookup"><span data-stu-id="901b1-128">Other configuration</span></span>

<span data-ttu-id="901b1-129">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="901b1-129">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="901b1-130">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-130">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="901b1-131">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="901b1-131">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="901b1-132"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="901b1-132">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="901b1-133">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="901b1-133">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="901b1-134">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="901b1-134">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="901b1-135">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="901b1-135">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="901b1-136">既定の構成</span><span class="sxs-lookup"><span data-stu-id="901b1-136">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="901b1-137">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-137">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="901b1-138">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-138">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="901b1-139">[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-139">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="901b1-140">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-140">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="901b1-141">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-141">Host configuration is provided from:</span></span>
  * <span data-ttu-id="901b1-142">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-142">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="901b1-143">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-143">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="901b1-144">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-144">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="901b1-145">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="901b1-145">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="901b1-146">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-146">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="901b1-147">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="901b1-147">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="901b1-148">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="901b1-148">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="901b1-149">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="901b1-149">Enable IIS integration.</span></span>
* <span data-ttu-id="901b1-150">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-150">App configuration is provided from:</span></span>
  * <span data-ttu-id="901b1-151">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="901b1-151">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-152">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="901b1-152">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-153">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="901b1-153">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="901b1-154">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-154">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-155">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="901b1-156">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-156">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="901b1-157">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-157">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="901b1-158">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-158">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="901b1-159">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-159">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="901b1-160">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-160">Host configuration is provided from:</span></span>
  * <span data-ttu-id="901b1-161">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-161">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="901b1-162">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-162">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="901b1-163">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-163">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="901b1-164">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-164">App configuration is provided from:</span></span>
  * <span data-ttu-id="901b1-165">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="901b1-165">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-166">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="901b1-166">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-167">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="901b1-167">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="901b1-168">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-168">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="901b1-169">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-169">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="901b1-170">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="901b1-170">Security</span></span>

<span data-ttu-id="901b1-171">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="901b1-171">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="901b1-172">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="901b1-172">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="901b1-173">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="901b1-173">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="901b1-174">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-174">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="901b1-175">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-175">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="901b1-176"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="901b1-176"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="901b1-177">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="901b1-177">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="901b1-178">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="901b1-178">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="901b1-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="901b1-180">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-180">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="901b1-181">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="901b1-181">Hierarchical configuration data</span></span>

<span data-ttu-id="901b1-182">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-182">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="901b1-183">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="901b1-183">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="901b1-184">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-184">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="901b1-185">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-185">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="901b1-186">section0:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-186">section0:key0</span></span>
* <span data-ttu-id="901b1-187">section0:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-187">section0:key1</span></span>
* <span data-ttu-id="901b1-188">section1:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-188">section1:key0</span></span>
* <span data-ttu-id="901b1-189">section1:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-189">section1:key1</span></span>

<span data-ttu-id="901b1-190"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-190"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="901b1-191">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="901b1-191">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="901b1-192">規約</span><span class="sxs-lookup"><span data-stu-id="901b1-192">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="901b1-193">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-193">Sources and providers</span></span>

<span data-ttu-id="901b1-194">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="901b1-194">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="901b1-195">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="901b1-195">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="901b1-196">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="901b1-196">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="901b1-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="901b1-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="901b1-199">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="901b1-199">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="901b1-200">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="901b1-200">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="901b1-201">キー</span><span class="sxs-lookup"><span data-stu-id="901b1-201">Keys</span></span>

<span data-ttu-id="901b1-202">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-202">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="901b1-203">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="901b1-203">Keys are case-insensitive.</span></span> <span data-ttu-id="901b1-204">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="901b1-204">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="901b1-205">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="901b1-205">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="901b1-206">階層キー</span><span class="sxs-lookup"><span data-stu-id="901b1-206">Hierarchical keys</span></span>
  * <span data-ttu-id="901b1-207">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="901b1-207">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="901b1-208">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="901b1-208">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="901b1-209">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-209">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="901b1-210">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="901b1-210">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="901b1-211">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="901b1-211">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="901b1-212"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="901b1-212">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="901b1-213">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="901b1-213">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="901b1-214">値</span><span class="sxs-lookup"><span data-stu-id="901b1-214">Values</span></span>

<span data-ttu-id="901b1-215">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-215">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="901b1-216">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="901b1-216">Values are strings.</span></span>
* <span data-ttu-id="901b1-217">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="901b1-217">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="901b1-218">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-218">Providers</span></span>

<span data-ttu-id="901b1-219">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="901b1-219">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="901b1-220">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-220">Provider</span></span> | <span data-ttu-id="901b1-221">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="901b1-221">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="901b1-222">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="901b1-222">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="901b1-223">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="901b1-223">Azure Key Vault</span></span> |
| <span data-ttu-id="901b1-224">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="901b1-224">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="901b1-225">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="901b1-225">Azure App Configuration</span></span> |
| [<span data-ttu-id="901b1-226">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-226">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="901b1-227">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="901b1-227">Command-line parameters</span></span> |
| [<span data-ttu-id="901b1-228">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-228">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="901b1-229">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="901b1-229">Custom source</span></span> |
| [<span data-ttu-id="901b1-230">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-230">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="901b1-231">環境変数</span><span class="sxs-lookup"><span data-stu-id="901b1-231">Environment variables</span></span> |
| [<span data-ttu-id="901b1-232">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-232">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="901b1-233">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="901b1-233">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="901b1-234">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-234">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="901b1-235">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="901b1-235">Directory files</span></span> |
| [<span data-ttu-id="901b1-236">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-236">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="901b1-237">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="901b1-237">In-memory collections</span></span> |
| <span data-ttu-id="901b1-238">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="901b1-238">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="901b1-239">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="901b1-239">File in the user profile directory</span></span> |

<span data-ttu-id="901b1-240">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="901b1-240">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="901b1-241">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="901b1-241">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="901b1-242">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="901b1-242">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="901b1-243">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="901b1-243">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="901b1-244">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="901b1-244">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="901b1-245">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="901b1-245">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="901b1-246">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="901b1-246">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="901b1-247">環境変数</span><span class="sxs-lookup"><span data-stu-id="901b1-247">Environment variables</span></span>
1. <span data-ttu-id="901b1-248">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="901b1-248">Command-line arguments</span></span>

<span data-ttu-id="901b1-249">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="901b1-249">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="901b1-250">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-250">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="901b1-251">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-251">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="901b1-252">ConfigureHostConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="901b1-252">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="901b1-253">ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-253">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="901b1-254">`ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-254">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="901b1-255">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-255">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="901b1-256">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="901b1-256">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="901b1-257">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-257">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="901b1-258">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="901b1-258">ConfigureAppConfiguration</span></span>

<span data-ttu-id="901b1-259">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-259">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="901b1-260">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="901b1-260">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="901b1-261">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-261">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="901b1-262">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="901b1-262">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="901b1-263">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-263">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="901b1-264">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="901b1-264">Consume configuration during app startup</span></span>

<span data-ttu-id="901b1-265">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-265">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="901b1-266">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-266">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="901b1-267">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-267">Command-line Configuration Provider</span></span>

<span data-ttu-id="901b1-268"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-268">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="901b1-269">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-269">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="901b1-270">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-270">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="901b1-271">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-271">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="901b1-272">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-272">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="901b1-273">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="901b1-273">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="901b1-274">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="901b1-274">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="901b1-275">環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-275">Environment variables.</span></span>

<span data-ttu-id="901b1-276">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="901b1-276">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="901b1-277">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="901b1-277">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="901b1-278">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="901b1-278">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="901b1-279">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-279">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="901b1-280">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="901b1-280">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="901b1-281">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-281">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="901b1-282">**例**</span><span class="sxs-lookup"><span data-stu-id="901b1-282">**Example**</span></span>

<span data-ttu-id="901b1-283">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-283">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="901b1-284">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-284">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="901b1-285">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="901b1-285">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="901b1-286">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-286">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="901b1-287">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="901b1-287">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="901b1-288">引数</span><span class="sxs-lookup"><span data-stu-id="901b1-288">Arguments</span></span>

<span data-ttu-id="901b1-289">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="901b1-289">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="901b1-290">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-290">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="901b1-291">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="901b1-291">Key prefix</span></span>               | <span data-ttu-id="901b1-292">例</span><span class="sxs-lookup"><span data-stu-id="901b1-292">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="901b1-293">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="901b1-293">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="901b1-294">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="901b1-294">Two dashes (`--`)</span></span>        | <span data-ttu-id="901b1-295">`--CommandLineKey2=value2`、`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="901b1-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="901b1-296">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="901b1-296">Forward slash (`/`)</span></span>      | <span data-ttu-id="901b1-297">`/CommandLineKey3=value3`、`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="901b1-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="901b1-298">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="901b1-298">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="901b1-299">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="901b1-299">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="901b1-300">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="901b1-300">Switch mappings</span></span>

<span data-ttu-id="901b1-301">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-301">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="901b1-302"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-302">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="901b1-303">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-303">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="901b1-304">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-304">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="901b1-305">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="901b1-305">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="901b1-306">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="901b1-306">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="901b1-307">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="901b1-307">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="901b1-308">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="901b1-308">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="901b1-309">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-309">Create a switch mappings dictionary.</span></span> <span data-ttu-id="901b1-310">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-310">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="901b1-311">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-311">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="901b1-312">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="901b1-312">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="901b1-313">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-313">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="901b1-314">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="901b1-315">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="901b1-316">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-316">Key</span></span>       | <span data-ttu-id="901b1-317">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="901b1-318">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="901b1-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="901b1-319">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="901b1-320">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-320">Key</span></span>               | <span data-ttu-id="901b1-321">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="901b1-322">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="901b1-323"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="901b1-324">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="901b1-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="901b1-326">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-326">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="901b1-327">新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-327">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="901b1-328">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-328">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="901b1-329">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-329">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="901b1-330">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-330">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="901b1-331">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="901b1-332">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="901b1-332">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="901b1-333">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="901b1-333">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="901b1-334">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="901b1-334">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="901b1-335">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-335">Command-line arguments.</span></span>

<span data-ttu-id="901b1-336">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-336">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="901b1-337">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-337">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="901b1-338">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-338">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="901b1-339">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="901b1-339">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="901b1-340">**例**</span><span class="sxs-lookup"><span data-stu-id="901b1-340">**Example**</span></span>

<span data-ttu-id="901b1-341">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-341">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="901b1-342">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-342">Run the sample app.</span></span> <span data-ttu-id="901b1-343">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-343">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="901b1-344">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="901b1-344">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="901b1-345">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="901b1-345">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="901b1-346">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-346">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="901b1-347">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-347">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="901b1-348">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="901b1-348">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="901b1-349">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="901b1-349">Prefixes</span></span>

<span data-ttu-id="901b1-350">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-350">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="901b1-351">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-351">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="901b1-352">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-352">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="901b1-353">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-353">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="901b1-354">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-354">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="901b1-355">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="901b1-355">**Connection string prefixes**</span></span>

<span data-ttu-id="901b1-356">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="901b1-356">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="901b1-357">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-357">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="901b1-358">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="901b1-358">Connection string prefix</span></span> | <span data-ttu-id="901b1-359">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-359">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="901b1-360">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-360">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="901b1-361">MySQL</span><span class="sxs-lookup"><span data-stu-id="901b1-361">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="901b1-362">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="901b1-362">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="901b1-363">SQL Server</span><span class="sxs-lookup"><span data-stu-id="901b1-363">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="901b1-364">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="901b1-364">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="901b1-365">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-365">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="901b1-366">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="901b1-366">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="901b1-367">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="901b1-367">Environment variable key</span></span> | <span data-ttu-id="901b1-368">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="901b1-368">Converted configuration key</span></span> | <span data-ttu-id="901b1-369">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="901b1-369">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="901b1-370">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="901b1-370">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="901b1-371">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="901b1-371">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="901b1-372">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="901b1-372">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="901b1-373">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="901b1-373">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="901b1-374">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="901b1-374">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="901b1-375">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="901b1-375">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="901b1-376">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="901b1-376">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="901b1-377">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-377">File Configuration Provider</span></span>

<span data-ttu-id="901b1-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="901b1-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="901b1-379">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="901b1-379">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="901b1-380">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-380">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="901b1-381">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-381">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="901b1-382">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-382">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="901b1-383">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-383">INI Configuration Provider</span></span>

<span data-ttu-id="901b1-384"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-384">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="901b1-385">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-385">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="901b1-386">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-386">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="901b1-387">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-387">Overloads permit specifying:</span></span>

* <span data-ttu-id="901b1-388">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-388">Whether the file is optional.</span></span>
* <span data-ttu-id="901b1-389">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-389">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="901b1-390">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-390">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="901b1-391">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-391">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="901b1-392">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="901b1-392">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="901b1-393">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-393">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="901b1-394">section0:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-394">section0:key0</span></span>
* <span data-ttu-id="901b1-395">section0:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-395">section0:key1</span></span>
* <span data-ttu-id="901b1-396">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="901b1-396">section1:subsection:key</span></span>
* <span data-ttu-id="901b1-397">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="901b1-397">section2:subsection0:key</span></span>
* <span data-ttu-id="901b1-398">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="901b1-398">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="901b1-399">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-399">JSON Configuration Provider</span></span>

<span data-ttu-id="901b1-400"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-400">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="901b1-401">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-401">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="901b1-402">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-402">Overloads permit specifying:</span></span>

* <span data-ttu-id="901b1-403">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-403">Whether the file is optional.</span></span>
* <span data-ttu-id="901b1-404">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-404">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="901b1-405">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-405">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="901b1-406">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-406">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="901b1-407">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-407">The method is called to load configuration from:</span></span>

* <span data-ttu-id="901b1-408">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="901b1-408">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="901b1-409">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="901b1-409">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="901b1-410">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-410">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="901b1-411">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-411">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="901b1-412">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-412">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="901b1-413">環境変数。</span><span class="sxs-lookup"><span data-stu-id="901b1-413">Environment variables.</span></span>
* <span data-ttu-id="901b1-414">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="901b1-414">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="901b1-415">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="901b1-415">Command-line arguments.</span></span>

<span data-ttu-id="901b1-416">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-416">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="901b1-417">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="901b1-417">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="901b1-418">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-418">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="901b1-419">**例**</span><span class="sxs-lookup"><span data-stu-id="901b1-419">**Example**</span></span>

<span data-ttu-id="901b1-420">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-420">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="901b1-421">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="901b1-421">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="901b1-422">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="901b1-422">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="901b1-423">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-423">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="901b1-424">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-424">Run the sample app.</span></span> <span data-ttu-id="901b1-425">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-425">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="901b1-426">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="901b1-426">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="901b1-427">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="901b1-427">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="901b1-428">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-428">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="901b1-429">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-429">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="901b1-430">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="901b1-430">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="901b1-431">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-431">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="901b1-432">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="901b1-432">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="901b1-433">キー `Logging:LogLevel:Default` のログ レベルは `Information` です。</span><span class="sxs-lookup"><span data-stu-id="901b1-433">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="901b1-434">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="901b1-434">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="901b1-435">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="901b1-435">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="901b1-436">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-436">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="901b1-437">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-437">Run the sample app.</span></span> <span data-ttu-id="901b1-438">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-438">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="901b1-439">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="901b1-439">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="901b1-440">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="901b1-440">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="901b1-441">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-441">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="901b1-442">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="901b1-442">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="901b1-443">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="901b1-443">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="901b1-444">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="901b1-444">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="901b1-445">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="901b1-445">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="901b1-446">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="901b1-446">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="901b1-447">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-447">XML Configuration Provider</span></span>

<span data-ttu-id="901b1-448"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-448">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="901b1-449">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-449">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="901b1-450">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="901b1-451">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-451">Whether the file is optional.</span></span>
* <span data-ttu-id="901b1-452">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="901b1-452">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="901b1-453">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-453">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="901b1-454">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-454">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="901b1-455">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="901b1-455">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="901b1-456">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-456">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="901b1-457">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-457">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="901b1-458">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-458">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="901b1-459">section0:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-459">section0:key0</span></span>
* <span data-ttu-id="901b1-460">section0:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-460">section0:key1</span></span>
* <span data-ttu-id="901b1-461">section1:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-461">section1:key0</span></span>
* <span data-ttu-id="901b1-462">section1:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-462">section1:key1</span></span>

<span data-ttu-id="901b1-463">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="901b1-463">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="901b1-464">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-464">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="901b1-465">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-465">section:section0:key:key0</span></span>
* <span data-ttu-id="901b1-466">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-466">section:section0:key:key1</span></span>
* <span data-ttu-id="901b1-467">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-467">section:section1:key:key0</span></span>
* <span data-ttu-id="901b1-468">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-468">section:section1:key:key1</span></span>

<span data-ttu-id="901b1-469">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-469">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="901b1-470">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-470">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="901b1-471">key:attribute</span><span class="sxs-lookup"><span data-stu-id="901b1-471">key:attribute</span></span>
* <span data-ttu-id="901b1-472">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="901b1-472">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="901b1-473">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-473">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="901b1-474"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-474">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="901b1-475">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="901b1-475">The key is the file name.</span></span> <span data-ttu-id="901b1-476">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-476">The value contains the file's contents.</span></span> <span data-ttu-id="901b1-477">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-477">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="901b1-478">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-478">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="901b1-479">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="901b1-479">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="901b1-480">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-480">Overloads permit specifying:</span></span>

* <span data-ttu-id="901b1-481">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="901b1-481">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="901b1-482">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="901b1-482">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="901b1-483">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-483">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="901b1-484">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-484">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="901b1-485">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-485">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="901b1-486">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-486">Memory Configuration Provider</span></span>

<span data-ttu-id="901b1-487"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-487">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="901b1-488">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-488">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="901b1-489">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-489">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="901b1-490">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-490">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="901b1-491">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-491">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="901b1-492">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-492">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="901b1-493">GetValue</span><span class="sxs-lookup"><span data-stu-id="901b1-493">GetValue</span></span>

<span data-ttu-id="901b1-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から単一の値が抽出され、それが指定したコレクション以外の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="901b1-495">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="901b1-495">An overload accepts a default value.</span></span>

<span data-ttu-id="901b1-496">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="901b1-496">The following example:</span></span>

* <span data-ttu-id="901b1-497">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-497">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="901b1-498">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-498">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="901b1-499">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="901b1-499">Types the value as an `int`.</span></span>
* <span data-ttu-id="901b1-500">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="901b1-500">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="901b1-501">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="901b1-501">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="901b1-502">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="901b1-502">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="901b1-503">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="901b1-503">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="901b1-504">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-504">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="901b1-505">section0:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-505">section0:key0</span></span>
* <span data-ttu-id="901b1-506">section0:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-506">section0:key1</span></span>
* <span data-ttu-id="901b1-507">section1:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-507">section1:key0</span></span>
* <span data-ttu-id="901b1-508">section1:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-508">section1:key1</span></span>
* <span data-ttu-id="901b1-509">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-509">section2:subsection0:key0</span></span>
* <span data-ttu-id="901b1-510">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-510">section2:subsection0:key1</span></span>
* <span data-ttu-id="901b1-511">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="901b1-511">section2:subsection1:key0</span></span>
* <span data-ttu-id="901b1-512">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="901b1-512">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="901b1-513">GetSection</span><span class="sxs-lookup"><span data-stu-id="901b1-513">GetSection</span></span>

<span data-ttu-id="901b1-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="901b1-515">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-515">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="901b1-516">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-516">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="901b1-517">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-517">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="901b1-518">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-518">`GetSection` never returns `null`.</span></span> <span data-ttu-id="901b1-519">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-519">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="901b1-520">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="901b1-520">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="901b1-521"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-521">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="901b1-522">GetChildren</span><span class="sxs-lookup"><span data-stu-id="901b1-522">GetChildren</span></span>

<span data-ttu-id="901b1-523">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-523">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="901b1-524">既存</span><span class="sxs-lookup"><span data-stu-id="901b1-524">Exists</span></span>

<span data-ttu-id="901b1-525">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="901b1-525">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="901b1-526">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="901b1-526">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="901b1-527">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="901b1-527">Bind to a class</span></span>

<span data-ttu-id="901b1-528">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-528">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="901b1-529">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-529">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="901b1-530">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-530">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="901b1-531">バインダーは、指定された型のすべてのパブリックな読み取り/書き込みプロパティに値をバインドします。</span><span class="sxs-lookup"><span data-stu-id="901b1-531">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="901b1-532">フィールドはバインド**されません**。</span><span class="sxs-lookup"><span data-stu-id="901b1-532">Fields are **not** bound.</span></span>

<span data-ttu-id="901b1-533">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-533">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="901b1-534">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-534">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="901b1-535">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-535">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="901b1-536">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-536">Key</span></span>                   | <span data-ttu-id="901b1-537">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-537">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="901b1-538">starship:name</span><span class="sxs-lookup"><span data-stu-id="901b1-538">starship:name</span></span>         | <span data-ttu-id="901b1-539">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="901b1-539">USS Enterprise</span></span>                                    |
| <span data-ttu-id="901b1-540">starship:registry</span><span class="sxs-lookup"><span data-stu-id="901b1-540">starship:registry</span></span>     | <span data-ttu-id="901b1-541">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="901b1-541">NCC-1701</span></span>                                          |
| <span data-ttu-id="901b1-542">starship:class</span><span class="sxs-lookup"><span data-stu-id="901b1-542">starship:class</span></span>        | <span data-ttu-id="901b1-543">Constitution</span><span class="sxs-lookup"><span data-stu-id="901b1-543">Constitution</span></span>                                      |
| <span data-ttu-id="901b1-544">starship:length</span><span class="sxs-lookup"><span data-stu-id="901b1-544">starship:length</span></span>       | <span data-ttu-id="901b1-545">304.8</span><span class="sxs-lookup"><span data-stu-id="901b1-545">304.8</span></span>                                             |
| <span data-ttu-id="901b1-546">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="901b1-546">starship:commissioned</span></span> | <span data-ttu-id="901b1-547">False</span><span class="sxs-lookup"><span data-stu-id="901b1-547">False</span></span>                                             |
| <span data-ttu-id="901b1-548">trademark</span><span class="sxs-lookup"><span data-stu-id="901b1-548">trademark</span></span>             | <span data-ttu-id="901b1-549">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="901b1-549">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="901b1-550">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="901b1-550">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="901b1-551">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-551">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="901b1-552">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-552">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="901b1-553">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="901b1-553">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="901b1-554">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="901b1-554">Bind to an object graph</span></span>

<span data-ttu-id="901b1-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="901b1-556">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="901b1-556">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="901b1-557">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="901b1-557">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="901b1-558">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-558">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="901b1-559">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="901b1-559">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="901b1-560">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="901b1-560">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="901b1-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="901b1-562">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="901b1-562">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="901b1-563">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="901b1-563">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="901b1-564">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="901b1-564">Bind an array to a class</span></span>

<span data-ttu-id="901b1-565">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="901b1-565">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="901b1-566"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="901b1-566">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="901b1-567">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="901b1-567">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="901b1-568">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-568">Binding is provided by convention.</span></span> <span data-ttu-id="901b1-569">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-569">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="901b1-570">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="901b1-570">**In-memory array processing**</span></span>

<span data-ttu-id="901b1-571">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="901b1-571">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="901b1-572">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-572">Key</span></span>             | <span data-ttu-id="901b1-573">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-573">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="901b1-574">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="901b1-574">array:entries:0</span></span> | <span data-ttu-id="901b1-575">value0</span><span class="sxs-lookup"><span data-stu-id="901b1-575">value0</span></span> |
| <span data-ttu-id="901b1-576">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="901b1-576">array:entries:1</span></span> | <span data-ttu-id="901b1-577">value1</span><span class="sxs-lookup"><span data-stu-id="901b1-577">value1</span></span> |
| <span data-ttu-id="901b1-578">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="901b1-578">array:entries:2</span></span> | <span data-ttu-id="901b1-579">value2</span><span class="sxs-lookup"><span data-stu-id="901b1-579">value2</span></span> |
| <span data-ttu-id="901b1-580">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="901b1-580">array:entries:4</span></span> | <span data-ttu-id="901b1-581">value4</span><span class="sxs-lookup"><span data-stu-id="901b1-581">value4</span></span> |
| <span data-ttu-id="901b1-582">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="901b1-582">array:entries:5</span></span> | <span data-ttu-id="901b1-583">value5</span><span class="sxs-lookup"><span data-stu-id="901b1-583">value5</span></span> |

<span data-ttu-id="901b1-584">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-584">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="901b1-585">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="901b1-585">The array skips a value for index &num;3.</span></span> <span data-ttu-id="901b1-586">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="901b1-586">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="901b1-587">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-587">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="901b1-588">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="901b1-588">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="901b1-589">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="901b1-589">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="901b1-590">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="901b1-590">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="901b1-591">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="901b1-591">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="901b1-592">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="901b1-592">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="901b1-593">0</span><span class="sxs-lookup"><span data-stu-id="901b1-593">0</span></span>                            | <span data-ttu-id="901b1-594">value0</span><span class="sxs-lookup"><span data-stu-id="901b1-594">value0</span></span>                       |
| <span data-ttu-id="901b1-595">1</span><span class="sxs-lookup"><span data-stu-id="901b1-595">1</span></span>                            | <span data-ttu-id="901b1-596">value1</span><span class="sxs-lookup"><span data-stu-id="901b1-596">value1</span></span>                       |
| <span data-ttu-id="901b1-597">2</span><span class="sxs-lookup"><span data-stu-id="901b1-597">2</span></span>                            | <span data-ttu-id="901b1-598">value2</span><span class="sxs-lookup"><span data-stu-id="901b1-598">value2</span></span>                       |
| <span data-ttu-id="901b1-599">3</span><span class="sxs-lookup"><span data-stu-id="901b1-599">3</span></span>                            | <span data-ttu-id="901b1-600">value4</span><span class="sxs-lookup"><span data-stu-id="901b1-600">value4</span></span>                       |
| <span data-ttu-id="901b1-601">4</span><span class="sxs-lookup"><span data-stu-id="901b1-601">4</span></span>                            | <span data-ttu-id="901b1-602">value5</span><span class="sxs-lookup"><span data-stu-id="901b1-602">value5</span></span>                       |

<span data-ttu-id="901b1-603">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-603">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="901b1-604">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-604">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="901b1-605">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="901b1-605">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="901b1-606">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-606">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="901b1-607">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="901b1-607">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="901b1-608">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="901b1-608">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="901b1-609">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="901b1-609">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="901b1-610">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-610">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="901b1-611">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-611">Key</span></span>             | <span data-ttu-id="901b1-612">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-612">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="901b1-613">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="901b1-613">array:entries:3</span></span> | <span data-ttu-id="901b1-614">value3</span><span class="sxs-lookup"><span data-stu-id="901b1-614">value3</span></span> |

<span data-ttu-id="901b1-615">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="901b1-615">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="901b1-616">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="901b1-616">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="901b1-617">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="901b1-617">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="901b1-618">0</span><span class="sxs-lookup"><span data-stu-id="901b1-618">0</span></span>                            | <span data-ttu-id="901b1-619">value0</span><span class="sxs-lookup"><span data-stu-id="901b1-619">value0</span></span>                       |
| <span data-ttu-id="901b1-620">1</span><span class="sxs-lookup"><span data-stu-id="901b1-620">1</span></span>                            | <span data-ttu-id="901b1-621">value1</span><span class="sxs-lookup"><span data-stu-id="901b1-621">value1</span></span>                       |
| <span data-ttu-id="901b1-622">2</span><span class="sxs-lookup"><span data-stu-id="901b1-622">2</span></span>                            | <span data-ttu-id="901b1-623">value2</span><span class="sxs-lookup"><span data-stu-id="901b1-623">value2</span></span>                       |
| <span data-ttu-id="901b1-624">3</span><span class="sxs-lookup"><span data-stu-id="901b1-624">3</span></span>                            | <span data-ttu-id="901b1-625">value3</span><span class="sxs-lookup"><span data-stu-id="901b1-625">value3</span></span>                       |
| <span data-ttu-id="901b1-626">4</span><span class="sxs-lookup"><span data-stu-id="901b1-626">4</span></span>                            | <span data-ttu-id="901b1-627">value4</span><span class="sxs-lookup"><span data-stu-id="901b1-627">value4</span></span>                       |
| <span data-ttu-id="901b1-628">5</span><span class="sxs-lookup"><span data-stu-id="901b1-628">5</span></span>                            | <span data-ttu-id="901b1-629">value5</span><span class="sxs-lookup"><span data-stu-id="901b1-629">value5</span></span>                       |

<span data-ttu-id="901b1-630">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="901b1-630">**JSON array processing**</span></span>

<span data-ttu-id="901b1-631">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-631">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="901b1-632">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="901b1-632">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="901b1-633">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="901b1-633">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="901b1-634">Key</span><span class="sxs-lookup"><span data-stu-id="901b1-634">Key</span></span>                     | <span data-ttu-id="901b1-635">[値]</span><span class="sxs-lookup"><span data-stu-id="901b1-635">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="901b1-636">json_array:key</span><span class="sxs-lookup"><span data-stu-id="901b1-636">json_array:key</span></span>          | <span data-ttu-id="901b1-637">valueA</span><span class="sxs-lookup"><span data-stu-id="901b1-637">valueA</span></span> |
| <span data-ttu-id="901b1-638">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="901b1-638">json_array:subsection:0</span></span> | <span data-ttu-id="901b1-639">valueB</span><span class="sxs-lookup"><span data-stu-id="901b1-639">valueB</span></span> |
| <span data-ttu-id="901b1-640">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="901b1-640">json_array:subsection:1</span></span> | <span data-ttu-id="901b1-641">valueC</span><span class="sxs-lookup"><span data-stu-id="901b1-641">valueC</span></span> |
| <span data-ttu-id="901b1-642">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="901b1-642">json_array:subsection:2</span></span> | <span data-ttu-id="901b1-643">valueD</span><span class="sxs-lookup"><span data-stu-id="901b1-643">valueD</span></span> |

<span data-ttu-id="901b1-644">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-644">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="901b1-645">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="901b1-645">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="901b1-646">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-646">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="901b1-647">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="901b1-647">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="901b1-648">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="901b1-648">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="901b1-649">0</span><span class="sxs-lookup"><span data-stu-id="901b1-649">0</span></span>                                   | <span data-ttu-id="901b1-650">valueB</span><span class="sxs-lookup"><span data-stu-id="901b1-650">valueB</span></span>                              |
| <span data-ttu-id="901b1-651">1</span><span class="sxs-lookup"><span data-stu-id="901b1-651">1</span></span>                                   | <span data-ttu-id="901b1-652">valueC</span><span class="sxs-lookup"><span data-stu-id="901b1-652">valueC</span></span>                              |
| <span data-ttu-id="901b1-653">2</span><span class="sxs-lookup"><span data-stu-id="901b1-653">2</span></span>                                   | <span data-ttu-id="901b1-654">valueD</span><span class="sxs-lookup"><span data-stu-id="901b1-654">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="901b1-655">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="901b1-655">Custom configuration provider</span></span>

<span data-ttu-id="901b1-656">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="901b1-656">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="901b1-657">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="901b1-657">The provider has the following characteristics:</span></span>

* <span data-ttu-id="901b1-658">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-658">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="901b1-659">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="901b1-659">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="901b1-660">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="901b1-660">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="901b1-661">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="901b1-661">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="901b1-662">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="901b1-662">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="901b1-663">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="901b1-663">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="901b1-664">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-664">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="901b1-665">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="901b1-665">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="901b1-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="901b1-667"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-667">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="901b1-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="901b1-669"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-669">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="901b1-670">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="901b1-670">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="901b1-671">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="901b1-671">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="901b1-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="901b1-673">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="901b1-674">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="901b1-675">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="901b1-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="901b1-676">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-676">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="901b1-677">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="901b1-677">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="901b1-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="901b1-679"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-679">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="901b1-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="901b1-681"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="901b1-681">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="901b1-682">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="901b1-682">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="901b1-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="901b1-684">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="901b1-684">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="901b1-685">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="901b1-685">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="901b1-686">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="901b1-686">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="901b1-687">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="901b1-687">Access configuration during startup</span></span>

<span data-ttu-id="901b1-688">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="901b1-688">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="901b1-689">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="901b1-689">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="901b1-690">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="901b1-690">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="901b1-691">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="901b1-691">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="901b1-692">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="901b1-692">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="901b1-693">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="901b1-693">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="901b1-694">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="901b1-694">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="901b1-695">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="901b1-695">Add configuration from an external assembly</span></span>

<span data-ttu-id="901b1-696"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="901b1-696">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="901b1-697">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="901b1-697">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="901b1-698">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="901b1-698">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
