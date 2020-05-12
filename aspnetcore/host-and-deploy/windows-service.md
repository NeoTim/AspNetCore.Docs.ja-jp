---
title: Windows サービスでの ASP.NET Core のホスト
author: rick-anderson
description: Windows サービスで ASP.NET Core アプリケーションをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4ad9086c60e58f89bdde4962d7487036df251cc1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776345"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="65c0f-103">Windows サービスでの ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="65c0f-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65c0f-104">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65c0f-105">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65c0f-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65c0f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65c0f-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="65c0f-107">Prerequisites</span></span>

* [<span data-ttu-id="65c0f-108">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65c0f-109">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="65c0f-110">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="65c0f-110">Worker Service template</span></span>

<span data-ttu-id="65c0f-111">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="65c0f-112">テンプレートを Windows サービス アプリの基礎として使用するには:</span><span class="sxs-lookup"><span data-stu-id="65c0f-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="65c0f-113">.NET Core テンプレートからワーカー サービス アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="65c0f-114">「[アプリの構成](#app-configuration)」セクションのガイダンスに従って、Windows サービスとして実行できるようにワーカー サービス アプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="65c0f-115">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65c0f-115">App configuration</span></span>

<span data-ttu-id="65c0f-116">アプリには、[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="65c0f-117">ホストのビルド時に `IHostBuilder.UseWindowsService` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="65c0f-118">アプリが Windows サービスとして実行している場合、メソッドは</span><span class="sxs-lookup"><span data-stu-id="65c0f-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="65c0f-119">ホストの有効期間を `WindowsServiceLifetime` に設定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="65c0f-120">[コンテンツ ルート](xref:fundamentals/index#content-root)を [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="65c0f-121">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="65c0f-122">イベント ログへのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="65c0f-123">アプリケーション名が既定のソース名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="65c0f-124">既定のログ レベルは、ホストを構築するために `CreateDefaultBuilder` が呼び出される ASP.NET Core テンプレートに基づくアプリに対して "*警告*" 以上です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="65c0f-125">既定のログ レベルを、*appsettings.json*/*appsettings.{環境}.json* の `Logging:EventLog:LogLevel:Default` キーまたは他の構成プロバイダーでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="65c0f-126">管理者のみが新しいイベント ソースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="65c0f-127">アプリケーション名を使用して、イベント ソースを作成できない場合、警告が*アプリケーション* ソースに記録され、イベント ログが無効になります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="65c0f-128">*Program.cs* の `CreateHostBuilder` の場合:</span><span class="sxs-lookup"><span data-stu-id="65c0f-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="65c0f-129">このトピックには、次のサンプル アプリが付属しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="65c0f-130">バックグラウンド ワーカー サービスのサンプル &ndash; バックグラウンド タスク用に[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する[ワーカー サービステンプレート](#worker-service-template)に基づく、非 Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="65c0f-131">Web アプリ サービスのサンプル &ndash; バックグラウンド タスク用の[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する Windows サービスとして実行される、Razor Pages Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="65c0f-132">MVC のガイダンスについては、「<xref:mvc/overview>」と「<xref:migration/22-to-30>」にある記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="65c0f-133">配置の種類</span><span class="sxs-lookup"><span data-stu-id="65c0f-133">Deployment type</span></span>

<span data-ttu-id="65c0f-134">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65c0f-135">SDK</span><span class="sxs-lookup"><span data-stu-id="65c0f-135">SDK</span></span>

<span data-ttu-id="65c0f-136">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65c0f-137">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65c0f-138">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65c0f-139">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65c0f-140">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65c0f-141">[Web SDK](#sdk) を使用している場合、*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65c0f-142">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65c0f-143">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65c0f-144">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65c0f-145">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65c0f-146">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65c0f-147">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65c0f-148">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65c0f-149">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65c0f-150">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="65c0f-151">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="65c0f-151">Service user account</span></span>

<span data-ttu-id="65c0f-152">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65c0f-153">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="65c0f-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-154">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="65c0f-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65c0f-155">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65c0f-156">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65c0f-157">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65c0f-158">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65c0f-159">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65c0f-160">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="65c0f-160">Log on as a service rights</span></span>

<span data-ttu-id="65c0f-161">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65c0f-162">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65c0f-163">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65c0f-164">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65c0f-165">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65c0f-166">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65c0f-167">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65c0f-168">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-168">Select **Advanced**.</span></span> <span data-ttu-id="65c0f-169">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-169">Select **Find Now**.</span></span> <span data-ttu-id="65c0f-170">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-170">Select the user account from the list.</span></span> <span data-ttu-id="65c0f-171">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-171">Select **OK**.</span></span> <span data-ttu-id="65c0f-172">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65c0f-173">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65c0f-174">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="65c0f-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65c0f-175">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-175">Create a service</span></span>

<span data-ttu-id="65c0f-176">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65c0f-177">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65c0f-178">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65c0f-179">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65c0f-180">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65c0f-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65c0f-182">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65c0f-183">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65c0f-184">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65c0f-185">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65c0f-186">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65c0f-187">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="65c0f-187">Start a service</span></span>

<span data-ttu-id="65c0f-188">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-189">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65c0f-190">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="65c0f-190">Determine a service's status</span></span>

<span data-ttu-id="65c0f-191">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="65c0f-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-192">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65c0f-193">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="65c0f-193">Stop a service</span></span>

<span data-ttu-id="65c0f-194">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65c0f-195">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="65c0f-195">Remove a service</span></span>

<span data-ttu-id="65c0f-196">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65c0f-197">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="65c0f-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65c0f-198">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65c0f-199">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65c0f-200">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-200">Configure endpoints</span></span>

<span data-ttu-id="65c0f-201">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65c0f-202">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65c0f-203">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65c0f-204">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65c0f-205">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65c0f-206">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65c0f-207">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="65c0f-207">Current directory and content root</span></span>

<span data-ttu-id="65c0f-208">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65c0f-209">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65c0f-210">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="65c0f-211">ContentRootPath または ContentRootFileProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="65c0f-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="65c0f-212">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) または <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> を使用して、アプリのリソースを見つけます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="65c0f-213">アプリがサービスとして実行されると、<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> によって <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> が [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="65c0f-214">アプリの既定設定ファイルである *appsettings.json* と *appsettings.{Environment}.json* は、[ホストの構築中に CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) を呼び出すことで、アプリのコンテンツ ルートから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="65c0f-215"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> の開発者コードによって読み込まれた他の設定ファイルについては、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="65c0f-216">次の例では、*custom_settings.json* ファイルはアプリのコンテンツ ルートに存在しており、明示的にベース パスを設定しなくても読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="65c0f-217">Windows サービス アプリからは *C:\\WINDOWS\\system32* フォルダーが現在のディレクトリとして返されるため、<xref:System.IO.Directory.GetCurrentDirectory*> を使用してリソース パスを取得しないでください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65c0f-218">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="65c0f-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65c0f-219">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65c0f-220">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="65c0f-220">Troubleshoot</span></span>

<span data-ttu-id="65c0f-221">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65c0f-222">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="65c0f-222">Common errors</span></span>

* <span data-ttu-id="65c0f-223">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65c0f-224">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65c0f-225">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65c0f-226">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65c0f-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65c0f-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65c0f-229">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65c0f-230">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65c0f-231">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65c0f-232">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65c0f-233">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65c0f-234">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65c0f-235">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65c0f-236">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="65c0f-236">System and Application Event Logs</span></span>

<span data-ttu-id="65c0f-237">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65c0f-238">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65c0f-239">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65c0f-240">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65c0f-241">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65c0f-242">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65c0f-243">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="65c0f-243">Run the app at a command prompt</span></span>

<span data-ttu-id="65c0f-244">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65c0f-245">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65c0f-246">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65c0f-247">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="65c0f-247">Clear package caches</span></span>

<span data-ttu-id="65c0f-248">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65c0f-249">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65c0f-250">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65c0f-251">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65c0f-252">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65c0f-253">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65c0f-254">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65c0f-255">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65c0f-256">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65c0f-257">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="65c0f-257">Slow or hanging app</span></span>

<span data-ttu-id="65c0f-258">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65c0f-259">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="65c0f-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="65c0f-260">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65c0f-261">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65c0f-262">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65c0f-263">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65c0f-264">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65c0f-265">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65c0f-266">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65c0f-267">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65c0f-268">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="65c0f-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65c0f-269">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65c0f-270">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="65c0f-270">Analyze the dump</span></span>

<span data-ttu-id="65c0f-271">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65c0f-272">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65c0f-273">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65c0f-273">Additional resources</span></span>

* <span data-ttu-id="65c0f-274">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="65c0f-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="65c0f-275">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65c0f-276">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65c0f-277">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65c0f-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65c0f-278">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="65c0f-278">Prerequisites</span></span>

* [<span data-ttu-id="65c0f-279">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65c0f-280">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="65c0f-281">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65c0f-281">App configuration</span></span>

<span data-ttu-id="65c0f-282">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="65c0f-283">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="65c0f-284">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="65c0f-285">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="65c0f-286">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="65c0f-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="65c0f-287"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="65c0f-288">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="65c0f-289">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="65c0f-290">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="65c0f-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="65c0f-291"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="65c0f-292">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="65c0f-293">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="65c0f-294">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="65c0f-295">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="65c0f-296">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="65c0f-297">配置の種類</span><span class="sxs-lookup"><span data-stu-id="65c0f-297">Deployment type</span></span>

<span data-ttu-id="65c0f-298">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65c0f-299">SDK</span><span class="sxs-lookup"><span data-stu-id="65c0f-299">SDK</span></span>

<span data-ttu-id="65c0f-300">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65c0f-301">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65c0f-302">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65c0f-303">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65c0f-304">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65c0f-305">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="65c0f-306">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="65c0f-307">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="65c0f-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="65c0f-308">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="65c0f-309">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65c0f-310">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65c0f-311">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65c0f-312">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65c0f-313">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65c0f-314">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65c0f-315">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65c0f-316">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65c0f-317">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65c0f-318">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="65c0f-319">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="65c0f-320">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="65c0f-320">Service user account</span></span>

<span data-ttu-id="65c0f-321">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65c0f-322">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="65c0f-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-323">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="65c0f-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65c0f-324">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65c0f-325">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65c0f-326">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65c0f-327">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65c0f-328">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65c0f-329">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="65c0f-329">Log on as a service rights</span></span>

<span data-ttu-id="65c0f-330">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65c0f-331">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65c0f-332">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65c0f-333">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65c0f-334">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65c0f-335">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65c0f-336">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65c0f-337">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-337">Select **Advanced**.</span></span> <span data-ttu-id="65c0f-338">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-338">Select **Find Now**.</span></span> <span data-ttu-id="65c0f-339">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-339">Select the user account from the list.</span></span> <span data-ttu-id="65c0f-340">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-340">Select **OK**.</span></span> <span data-ttu-id="65c0f-341">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65c0f-342">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65c0f-343">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="65c0f-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65c0f-344">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-344">Create a service</span></span>

<span data-ttu-id="65c0f-345">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65c0f-346">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65c0f-347">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65c0f-348">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65c0f-349">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65c0f-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65c0f-351">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65c0f-352">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65c0f-353">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65c0f-354">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65c0f-355">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65c0f-356">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="65c0f-356">Start a service</span></span>

<span data-ttu-id="65c0f-357">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-358">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65c0f-359">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="65c0f-359">Determine a service's status</span></span>

<span data-ttu-id="65c0f-360">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="65c0f-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-361">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65c0f-362">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="65c0f-362">Stop a service</span></span>

<span data-ttu-id="65c0f-363">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65c0f-364">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="65c0f-364">Remove a service</span></span>

<span data-ttu-id="65c0f-365">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="65c0f-366">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="65c0f-366">Handle starting and stopping events</span></span>

<span data-ttu-id="65c0f-367"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="65c0f-368">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="65c0f-369">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="65c0f-370">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="65c0f-371">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65c0f-372">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="65c0f-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65c0f-373">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65c0f-374">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65c0f-375">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-375">Configure endpoints</span></span>

<span data-ttu-id="65c0f-376">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65c0f-377">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65c0f-378">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65c0f-379">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65c0f-380">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65c0f-381">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65c0f-382">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="65c0f-382">Current directory and content root</span></span>

<span data-ttu-id="65c0f-383">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65c0f-384">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65c0f-385">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="65c0f-386">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="65c0f-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="65c0f-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="65c0f-388">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="65c0f-389">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65c0f-390">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="65c0f-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65c0f-391">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65c0f-392">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="65c0f-392">Troubleshoot</span></span>

<span data-ttu-id="65c0f-393">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65c0f-394">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="65c0f-394">Common errors</span></span>

* <span data-ttu-id="65c0f-395">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65c0f-396">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65c0f-397">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65c0f-398">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65c0f-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65c0f-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65c0f-401">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65c0f-402">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65c0f-403">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65c0f-404">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65c0f-405">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65c0f-406">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65c0f-407">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65c0f-408">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="65c0f-408">System and Application Event Logs</span></span>

<span data-ttu-id="65c0f-409">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65c0f-410">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65c0f-411">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65c0f-412">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65c0f-413">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65c0f-414">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65c0f-415">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="65c0f-415">Run the app at a command prompt</span></span>

<span data-ttu-id="65c0f-416">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65c0f-417">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65c0f-418">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65c0f-419">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="65c0f-419">Clear package caches</span></span>

<span data-ttu-id="65c0f-420">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65c0f-421">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65c0f-422">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65c0f-423">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65c0f-424">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65c0f-425">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65c0f-426">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65c0f-427">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65c0f-428">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65c0f-429">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="65c0f-429">Slow or hanging app</span></span>

<span data-ttu-id="65c0f-430">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65c0f-431">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="65c0f-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="65c0f-432">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65c0f-433">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65c0f-434">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65c0f-435">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65c0f-436">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65c0f-437">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65c0f-438">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65c0f-439">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65c0f-440">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="65c0f-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65c0f-441">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65c0f-442">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="65c0f-442">Analyze the dump</span></span>

<span data-ttu-id="65c0f-443">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65c0f-444">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65c0f-445">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65c0f-445">Additional resources</span></span>

* <span data-ttu-id="65c0f-446">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="65c0f-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="65c0f-447">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="65c0f-448">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="65c0f-449">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65c0f-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="65c0f-450">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="65c0f-450">Prerequisites</span></span>

* [<span data-ttu-id="65c0f-451">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="65c0f-452">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="65c0f-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="65c0f-453">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="65c0f-453">App configuration</span></span>

<span data-ttu-id="65c0f-454">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="65c0f-455">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="65c0f-456">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="65c0f-457">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="65c0f-458">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="65c0f-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="65c0f-459"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="65c0f-460">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="65c0f-461">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="65c0f-462">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="65c0f-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="65c0f-463"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="65c0f-464">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="65c0f-465">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="65c0f-466">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="65c0f-467">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="65c0f-468">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="65c0f-469">配置の種類</span><span class="sxs-lookup"><span data-stu-id="65c0f-469">Deployment type</span></span>

<span data-ttu-id="65c0f-470">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="65c0f-471">SDK</span><span class="sxs-lookup"><span data-stu-id="65c0f-471">SDK</span></span>

<span data-ttu-id="65c0f-472">Razor Pages または MVC フレームワークを使用する Web アプリ ベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="65c0f-473">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="65c0f-474">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="65c0f-475">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="65c0f-476">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="65c0f-477">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="65c0f-478">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="65c0f-479">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="65c0f-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="65c0f-480">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="65c0f-481">`<UseAppHost>` プロパティが `true` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="65c0f-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="65c0f-482">このプロパティによって、サービスに FDD のアクティブ化パス (実行可能ファイル、 *.exe*) を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="65c0f-483">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="65c0f-484">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="65c0f-485">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="65c0f-486">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="65c0f-487">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="65c0f-488">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="65c0f-489">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="65c0f-490">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="65c0f-491">プロパティ名 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="65c0f-492">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="65c0f-493">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="65c0f-494">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="65c0f-494">Service user account</span></span>

<span data-ttu-id="65c0f-495">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="65c0f-496">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="65c0f-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-497">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="65c0f-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="65c0f-498">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="65c0f-499">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="65c0f-500">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="65c0f-501">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="65c0f-502">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="65c0f-503">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="65c0f-503">Log on as a service rights</span></span>

<span data-ttu-id="65c0f-504">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="65c0f-505">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="65c0f-506">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="65c0f-507">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="65c0f-508">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="65c0f-509">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="65c0f-510">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="65c0f-511">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-511">Select **Advanced**.</span></span> <span data-ttu-id="65c0f-512">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-512">Select **Find Now**.</span></span> <span data-ttu-id="65c0f-513">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-513">Select the user account from the list.</span></span> <span data-ttu-id="65c0f-514">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-514">Select **OK**.</span></span> <span data-ttu-id="65c0f-515">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="65c0f-516">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="65c0f-517">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="65c0f-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="65c0f-518">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-518">Create a service</span></span>

<span data-ttu-id="65c0f-519">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="65c0f-520">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="65c0f-521">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="65c0f-522">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="65c0f-523">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="65c0f-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="65c0f-525">`{SERVICE NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="65c0f-526">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="65c0f-527">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="65c0f-528">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="65c0f-529">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="65c0f-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="65c0f-530">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="65c0f-530">Start a service</span></span>

<span data-ttu-id="65c0f-531">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-532">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="65c0f-533">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="65c0f-533">Determine a service's status</span></span>

<span data-ttu-id="65c0f-534">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="65c0f-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="65c0f-535">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="65c0f-536">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="65c0f-536">Stop a service</span></span>

<span data-ttu-id="65c0f-537">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="65c0f-538">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="65c0f-538">Remove a service</span></span>

<span data-ttu-id="65c0f-539">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="65c0f-540">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="65c0f-540">Handle starting and stopping events</span></span>

<span data-ttu-id="65c0f-541"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="65c0f-542">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="65c0f-543">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="65c0f-544">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="65c0f-545">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65c0f-546">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="65c0f-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65c0f-547">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="65c0f-548">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="65c0f-549">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="65c0f-549">Configure endpoints</span></span>

<span data-ttu-id="65c0f-550">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="65c0f-551">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="65c0f-552">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="65c0f-553">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="65c0f-554">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="65c0f-555">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="65c0f-556">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="65c0f-556">Current directory and content root</span></span>

<span data-ttu-id="65c0f-557">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="65c0f-558">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="65c0f-559">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="65c0f-560">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="65c0f-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="65c0f-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="65c0f-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="65c0f-562">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="65c0f-563">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="65c0f-564">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="65c0f-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="65c0f-565">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="65c0f-566">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="65c0f-566">Troubleshoot</span></span>

<span data-ttu-id="65c0f-567">Windows サービス アプリのトラブルシューティングについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="65c0f-568">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="65c0f-568">Common errors</span></span>

* <span data-ttu-id="65c0f-569">PowerShell の以前のバージョンまたはプレリリース バージョンが使用されています。</span><span class="sxs-lookup"><span data-stu-id="65c0f-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="65c0f-570">登録されたサービスに、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドからのアプリの**発行済み**出力が使用されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="65c0f-571">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドの出力が、アプリの展開でサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="65c0f-572">発行された資産は、展開の種類に応じて次のいずれかのフォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="65c0f-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="65c0f-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="65c0f-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="65c0f-575">サービスが実行中の状態ではありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="65c0f-576">アプリに使用されるリソース (証明書など) のパスが正しくありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="65c0f-577">Windows サービスのベース パスは *c:\\Windows\\System32* です。</span><span class="sxs-lookup"><span data-stu-id="65c0f-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="65c0f-578">"*サービスとしてログオンする*" アクセス権がユーザーにありません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="65c0f-579">`New-Service` PowerShell コマンドの実行時に、ユーザーのパスワードの有効期限が切れていたか、正しく渡されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="65c0f-580">アプリに ASP.NET Core 認証が必要ですが、セキュリティで保護された接続 (HTTPS) 用に構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="65c0f-581">要求 URL ポートが正しくないか、アプリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="65c0f-582">システム イベント ログとアプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="65c0f-582">System and Application Event Logs</span></span>

<span data-ttu-id="65c0f-583">システム イベント ログとアプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="65c0f-584">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="65c0f-585">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="65c0f-586">**[システム]** を選択してシステム イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="65c0f-587">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="65c0f-588">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="65c0f-589">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="65c0f-589">Run the app at a command prompt</span></span>

<span data-ttu-id="65c0f-590">多くのスタートアップ エラーでは、イベント ログに有用な情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="65c0f-591">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="65c0f-592">アプリからさらに詳細情報をログに記録するには、[ログ レベル](xref:fundamentals/logging/index#log-level)を低くするか、[開発環境](xref:fundamentals/environments)でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="65c0f-593">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="65c0f-593">Clear package caches</span></span>

<span data-ttu-id="65c0f-594">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="65c0f-595">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="65c0f-596">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="65c0f-597">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="65c0f-598">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="65c0f-599">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="65c0f-600">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="65c0f-601">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="65c0f-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="65c0f-602">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="65c0f-603">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="65c0f-603">Slow or hanging app</span></span>

<span data-ttu-id="65c0f-604">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="65c0f-605">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="65c0f-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="65c0f-606">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="65c0f-607">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="65c0f-608">次のアプリケーションの実行可能ファイル名で [EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="65c0f-609">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="65c0f-610">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="65c0f-611">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="65c0f-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="65c0f-612">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="65c0f-613">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="65c0f-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="65c0f-614">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="65c0f-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="65c0f-615">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="65c0f-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="65c0f-616">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="65c0f-616">Analyze the dump</span></span>

<span data-ttu-id="65c0f-617">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="65c0f-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="65c0f-618">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65c0f-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65c0f-619">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65c0f-619">Additional resources</span></span>

* <span data-ttu-id="65c0f-620">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="65c0f-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
