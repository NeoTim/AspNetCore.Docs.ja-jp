---
title: ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する
author: guardrex
description: '[IdentityServer](https://identityserver.io/) バックエンドを使用して、Visual Studio 内から認証を用いて新しい Blazor ホスト型アプリを作成するには'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0fdc88e8e50856fcc4da0beb74f03925ae24401e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103401"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="de433-103">ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="de433-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="de433-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="de433-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="de433-105">この記事では、ユーザーと API 呼び出しの認証に [IdentityServer](https://identityserver.io/) を使用する、Blazor でホストされたアプリを新しく作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="de433-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de433-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de433-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de433-107">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="de433-107">In Visual Studio:</span></span>

1. <span data-ttu-id="de433-108">新しい **Blazor WebAssembly** アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="de433-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="de433-109">詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="de433-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="de433-110">**[新しい Blazor アプリを作成します]** ダイアログで、 **[認証]** セクションの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="de433-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="de433-111">**[個人のユーザー アカウント]** を選択し、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="de433-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="de433-112">**[詳細設定]** セクションで、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="de433-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="de433-113">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="de433-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de433-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de433-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="de433-115">コマンド シェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="de433-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="de433-116">出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="de433-117">また、フォルダー名は、プロジェクトの名前の一部となります。</span><span class="sxs-lookup"><span data-stu-id="de433-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="de433-118">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="de433-118">Server app configuration</span></span>

<span data-ttu-id="de433-119">次のセクションでは、認証のサポートが含まれる場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="de433-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="de433-120">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="de433-120">Startup class</span></span>

<span data-ttu-id="de433-121">`Startup` クラスには次のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="de433-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="de433-122">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="de433-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="de433-123">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="de433-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="de433-124">IdentityServer 上に既定の ASP.NET Core 規則を設定する <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドが追加された IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="de433-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="de433-125">IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドが追加された Authentication。</span><span class="sxs-lookup"><span data-stu-id="de433-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="de433-126">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="de433-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="de433-127">IdentityServer のミドルウェアでは、Open ID Connect (OIDC) のエンドポイントが公開されます。</span><span class="sxs-lookup"><span data-stu-id="de433-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="de433-128">認証ミドルウェアでは、要求の資格情報の検証と、要求コンテキストでのユーザーの設定が行われます。</span><span class="sxs-lookup"><span data-stu-id="de433-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="de433-129">承認ミドルウェアにより、承認機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="de433-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="de433-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="de433-130">AddApiAuthorization</span></span>

<span data-ttu-id="de433-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドでは、ASP.NET Core シナリオ対応に [IdentityServer](https://identityserver.io/) が構成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="de433-132">IdentityServer は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="de433-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="de433-133">IdentityServer を使用すると、ほとんどの一般的なシナリオには必要のない複雑さが発生します。</span><span class="sxs-lookup"><span data-stu-id="de433-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="de433-134">そのため、使用開始時に適切であると考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="de433-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="de433-135">認証のニーズが変わったら、IdentityServer のあらゆる機能を利用し、アプリの要件に合わせて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="de433-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="de433-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="de433-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="de433-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドでは、アプリに対するポリシー スキームが既定の認証ハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="de433-138">そのポリシーは、Identity の URL 空間 `/Identity` のサブパスにルーティングされたすべての要求を Identity で処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="de433-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="de433-139">それ以外のすべての要求は、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> で処理されます。</span><span class="sxs-lookup"><span data-stu-id="de433-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="de433-140">さらに、このメソッドでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="de433-140">Additionally, this method:</span></span>

* <span data-ttu-id="de433-141">既定のスコープ `{APPLICATION NAME}API` を使用して、`{APPLICATION NAME}API` API リソースが IdentityServer に登録されます。</span><span class="sxs-lookup"><span data-stu-id="de433-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="de433-142">IdentityServer によってアプリに対して発行されたトークンを検証するように、JWT ベアラー トークン ミドルウェアが構成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="de433-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="de433-143">WeatherForecastController</span></span>

<span data-ttu-id="de433-144">`WeatherForecastController` (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="de433-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="de433-145">その属性では、ユーザーはリソースにアクセスするために既定のポリシーに基づいて承認される必要があることが示されています。</span><span class="sxs-lookup"><span data-stu-id="de433-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="de433-146">既定の承認ポリシーは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> によって設定される既定の認証スキームを使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="de433-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="de433-147">ヘルパー メソッドでは、アプリへの要求に対する既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="de433-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="de433-148">ApplicationDbContext</span></span>

<span data-ttu-id="de433-149">`ApplicationDbContext` (*Data/ApplicationDbContext.cs*) では、<xref:Microsoft.EntityFrameworkCore.DbContext> により、IdentityServer 用のスキーマを含むように <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> が拡張されます。</span><span class="sxs-lookup"><span data-stu-id="de433-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="de433-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="de433-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="de433-151">データベース スキーマを完全に制御するには、使用可能な Identity <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの 1 つを継承し、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出すことによって、Identity スキーマを含むようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="de433-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="de433-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="de433-152">OidcConfigurationController</span></span>

<span data-ttu-id="de433-153">`OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) では、OIDC パラメーターを提供するようにクライアント エンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="de433-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="de433-154">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="de433-154">App settings files</span></span>

<span data-ttu-id="de433-155">プロジェクト ルートにあるアプリ設定ファイル (*appsettings*) の `IdentityServer` セクションには、構成されているクライアントの一覧が記述されてます。</span><span class="sxs-lookup"><span data-stu-id="de433-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="de433-156">次の例には、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="de433-156">In the following example, there's a single client.</span></span> <span data-ttu-id="de433-157">クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="de433-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="de433-158">構成対象のアプリの種類は、プロファイルによって示されています。</span><span class="sxs-lookup"><span data-stu-id="de433-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="de433-159">プロファイルは、サーバーの構成プロセスを簡素化する規則を促進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="de433-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="de433-160">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.Client`)。</span><span class="sxs-lookup"><span data-stu-id="de433-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="de433-161">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="de433-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="de433-162">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="de433-162">Authentication package</span></span>

<span data-ttu-id="de433-163">個人のユーザー アカウント (`Individual`) を使用するように作成されているアプリは、そのアプリのプロジェクト ファイル内で [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="de433-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="de433-164">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="de433-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="de433-165">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="de433-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="de433-166">API の承認のサポート</span><span class="sxs-lookup"><span data-stu-id="de433-166">API authorization support</span></span>

<span data-ttu-id="de433-167">ユーザーの認証のサポートは、[Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージに提供される拡張メソッドによって、サービス コンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="de433-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="de433-168">このメソッドでは、アプリが既存の承認システムとやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="de433-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="de433-169">既定では、アプリの構成は規則により `_configuration/{client-id}` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="de433-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="de433-170">規則により、アプリのアセンブリ名にはクライアント ID が設定されます。</span><span class="sxs-lookup"><span data-stu-id="de433-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="de433-171">オプションを指定してオーバーロードを呼び出すことにより、別のエンドポイントを指すようにこの URL を変更できます。</span><span class="sxs-lookup"><span data-stu-id="de433-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="de433-172">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="de433-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="de433-173">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="de433-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="de433-174">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="de433-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="de433-175">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="de433-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="de433-176">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="de433-176">LoginDisplay component</span></span>

<span data-ttu-id="de433-177">`LoginDisplay` コンポーネント (*Shared/LoginDisplay.razor*) は、`MainLayout` コンポーネント (*Shared/MainLayout. razor*) でレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="de433-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="de433-178">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="de433-178">For authenticated users:</span></span>
  * <span data-ttu-id="de433-179">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="de433-179">Displays the current user name.</span></span>
  * <span data-ttu-id="de433-180">ASP.NET Core Identity のユーザー プロファイル ページへのリンクが提供されます。</span><span class="sxs-lookup"><span data-stu-id="de433-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="de433-181">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="de433-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="de433-182">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="de433-182">For anonymous users:</span></span>
  * <span data-ttu-id="de433-183">登録するオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="de433-183">Offers the option to register.</span></span>
  * <span data-ttu-id="de433-184">ログインするオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="de433-184">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="de433-185">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="de433-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="de433-186">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="de433-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="de433-187">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="de433-187">Run the app</span></span>

<span data-ttu-id="de433-188">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="de433-188">Run the app from the Server project.</span></span> <span data-ttu-id="de433-189">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="de433-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="de433-190">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="de433-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="de433-191">**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="de433-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="de433-192">API 認証での名前とロール要求</span><span class="sxs-lookup"><span data-stu-id="de433-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="de433-193">カスタム ユーザー ファクトリ</span><span class="sxs-lookup"><span data-stu-id="de433-193">Custom user factory</span></span>

<span data-ttu-id="de433-194">クライアント アプリで、カスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="de433-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="de433-195"> Server により、複数のロールが JSON 配列として 1 つの `role` 要求で送信されます。</span><span class="sxs-lookup"><span data-stu-id="de433-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="de433-196">1 つのロールは、要求内で文字列値として送信されます。</span><span class="sxs-lookup"><span data-stu-id="de433-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="de433-197">ファクトリにより、ユーザーのロールごとに個別の `role` 要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="de433-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="de433-198">*CustomUserFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="de433-198">*CustomUserFactory.cs*:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="de433-199">クライアント アプリでは、`Program.Main` (*Program.cs*) にファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="de433-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="de433-200">サーバー アプリでは、Identity ビルダーで <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> を呼び出します。これにより、ロールに関連するサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="de433-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="de433-201">Identity Server を構成する</span><span class="sxs-lookup"><span data-stu-id="de433-201">Configure Identity Server</span></span>

<span data-ttu-id="de433-202">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="de433-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="de433-203">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="de433-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="de433-204">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="de433-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="de433-205">API 承認のオプション</span><span class="sxs-lookup"><span data-stu-id="de433-205">API authorization options</span></span>

<span data-ttu-id="de433-206">サーバー アプリで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="de433-206">In the Server app:</span></span>

* <span data-ttu-id="de433-207">`name` 要求と `role` 要求を ID トークンとアクセス トークンに挿入するように、Identity Server を構成します。</span><span class="sxs-lookup"><span data-stu-id="de433-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="de433-208">JWT トークン ハンドラーでロールの既定のマッピングを禁止します。</span><span class="sxs-lookup"><span data-stu-id="de433-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="de433-209">プロファイル サービス</span><span class="sxs-lookup"><span data-stu-id="de433-209">Profile Service</span></span>

<span data-ttu-id="de433-210">サーバー アプリで、`ProfileService` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="de433-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="de433-211">*ProfileService.cs*:</span><span class="sxs-lookup"><span data-stu-id="de433-211">*ProfileService.cs*:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="de433-212">サーバー アプリで、`Startup.ConfigureServices` にプロファイル サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="de433-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="de433-213">承認メカニズムを使用する</span><span class="sxs-lookup"><span data-stu-id="de433-213">Use authorization mechanisms</span></span>

<span data-ttu-id="de433-214">クライアント アプリでは、この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="de433-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="de433-215">コンポーネント内のすべての承認メカニズムで、ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="de433-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="de433-216">[AuthorizeView コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="de433-216">[AuthorizeView component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="de433-217">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="de433-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="de433-218">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="de433-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="de433-219">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="de433-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="de433-220">`User.Identity.Name` には、クライアント アプリにおいてユーザーのユーザー名が設定されます。これは通常、サインイン メール アドレスです。</span><span class="sxs-lookup"><span data-stu-id="de433-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="de433-221">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="de433-221">Additional resources</span></span>

* [<span data-ttu-id="de433-222">Azure App Service にデプロイする</span><span class="sxs-lookup"><span data-stu-id="de433-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="de433-223">Key Vault から証明書をインポートする (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="de433-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="de433-224">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="de433-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)