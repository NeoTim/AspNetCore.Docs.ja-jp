---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: ASP.NET Core Identityアプリ内でホストされるシングルページアプリで使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 178f85df0d35027cddb4314f9dabe26af8483ce6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775675"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="fd5cc-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="fd5cc-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="fd5cc-104">ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="fd5cc-105">ユーザー Identityを認証および格納するための ASP.NET Core は、Open ID Connect を実装する[ために、ユーザーと組み合わせ](https://identityserver.io/)て使用されます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="fd5cc-106">認証パラメーターが、 **web アプリケーション (モデルビューコントローラー)** (MVC) および**web アプリケーション**(Razorページ) プロジェクトテンプレートの認証パラメーターに似た**角度**で、**応答**するプロジェクトテンプレートに追加されました。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="fd5cc-107">許可されるパラメーター値は、 **None**および**個人**です。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="fd5cc-108">この時点では、対応する **.js および Redux**プロジェクトテンプレートで認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="fd5cc-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="fd5cc-109">Create an app with API authorization support</span></span>

<span data-ttu-id="fd5cc-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="fd5cc-111">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="fd5cc-112">**角度**:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="fd5cc-113">**反応**:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="fd5cc-114">前のコマンドは、 *ClientApp*ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="fd5cc-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="fd5cc-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="fd5cc-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="fd5cc-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="fd5cc-117">Startup class</span></span>

<span data-ttu-id="fd5cc-118">クラス`Startup`には、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="fd5cc-119">`Startup.ConfigureServices`メソッド内:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="fd5cc-120">既定の UI では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-120"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="fd5cc-121">サーバーには、次`AddApiAuthorization`のように、既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-121">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="fd5cc-122">次のように`AddIdentityServerJwt`追加のヘルパーメソッドを使用して認証します。これにより、アプリは、サービスによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="fd5cc-123">`Startup.Configure`メソッド内:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="fd5cc-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="fd5cc-125">Open ID Connect エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="fd5cc-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="fd5cc-126">AddApiAuthorization</span></span>

<span data-ttu-id="fd5cc-127">このヘルパーメソッドは、サポートされる構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="fd5cc-128">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="fd5cc-129">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="fd5cc-130">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="fd5cc-131">認証を変更する必要がある場合でも、ユーザーのニーズに合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="fd5cc-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="fd5cc-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="fd5cc-133">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="fd5cc-134">このポリシーは、 Identity Identity URL スペース "/Identity" のサブパスにルーティングされるすべての要求を処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="fd5cc-135">は`JwtBearerHandler` 、他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="fd5cc-136">さらに、このメソッドは`<<ApplicationName>>API` API リソースをの既定の`<<ApplicationName>>API`スコープに登録し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="fd5cc-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="fd5cc-137">WeatherForecastController</span></span>

<span data-ttu-id="fd5cc-138">*Controllers\WeatherForecastController.cs*ファイルで、属性が`[Authorize]`クラスに適用されていることを確認します。これは、リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="fd5cc-139">既定の承認ポリシーは、前に説明したポリシースキームによって`AddIdentityServerJwt`設定される既定の認証スキームを使用するように構成され`JwtBearerHandler` 、このようなヘルパーメソッドによって構成されたは、アプリに対する要求の既定のハンドラーになります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="fd5cc-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="fd5cc-140">ApplicationDbContext</span></span>

<span data-ttu-id="fd5cc-141">*Data\ApplicationDbContext.cs*ファイルでは、 `DbContext`が拡張Identity `ApiAuthorizationDbContext`する例外 (から`IdentityDbContext`派生したクラス) を使用して、で同じを使用します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="fd5cc-142">データベーススキーマを完全に制御するには、使用Identity `DbContext`可能なクラスの1つを継承し、 Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating`メソッドでを呼び出してスキーマを含めるようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="fd5cc-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="fd5cc-143">OidcConfigurationController</span></span>

<span data-ttu-id="fd5cc-144">*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fd5cc-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="fd5cc-145">appsettings.json</span></span>

<span data-ttu-id="fd5cc-146">プロジェクトルートの*appsettings*ファイルには、構成されたクライアントの一覧`IdentityServer`を説明する新しいセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="fd5cc-147">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-147">In the following example, there's a single client.</span></span> <span data-ttu-id="fd5cc-148">クライアント名はアプリケーション名に対応し、OAuth `ClientId`パラメーターに規約によってマップされます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="fd5cc-149">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="fd5cc-150">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="fd5cc-151">「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="fd5cc-152">appsettings.開発. json</span><span class="sxs-lookup"><span data-stu-id="fd5cc-152">appsettings.Development.json</span></span>

<span data-ttu-id="fd5cc-153">Appsettings で *。プロジェクトルートの開発用 json*ファイルには、トークンの`IdentityServer`署名に使用されるキーについて説明するセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="fd5cc-154">運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="fd5cc-155">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="fd5cc-155">General description of the Angular app</span></span>

<span data-ttu-id="fd5cc-156">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの*Clientapp-authorization*ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="fd5cc-157">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="fd5cc-158">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-158">3 components:</span></span>
  * <span data-ttu-id="fd5cc-159">*login. component. ts*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="fd5cc-160">*logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="fd5cc-161">*login-menu. component. ts*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="fd5cc-162">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="fd5cc-163">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="fd5cc-164">ルートに追加`AuthorizeGuard`できるルートガード。ルートにアクセスする前にユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="fd5cc-165">ユーザーが認証`AuthorizeInterceptor`されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="fd5cc-166">認証プロセス`AuthorizeService`の下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="fd5cc-167">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="fd5cc-168">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="fd5cc-169">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="fd5cc-169">General description of the React app</span></span>

<span data-ttu-id="fd5cc-170">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="fd5cc-171">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="fd5cc-172">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-172">4 components:</span></span>
  * <span data-ttu-id="fd5cc-173">*.Js*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="fd5cc-174">*Logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="fd5cc-175">*Loginmenu js*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="fd5cc-176">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="fd5cc-177">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="fd5cc-178">*AuthorizeRoute*: `Component`パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="fd5cc-179">認証プロセス`authService`の下位レベル`AuthorizeService`の詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="fd5cc-180">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="fd5cc-181">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="fd5cc-181">Require authorization on a new API</span></span>

<span data-ttu-id="fd5cc-182">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="fd5cc-183">これを行うには、新しいコントローラーを作成し`[Authorize]` 、コントローラークラスまたはコントローラー内の任意のアクションに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="fd5cc-184">API 認証ハンドラーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="fd5cc-184">Customize the API authentication handler</span></span>

<span data-ttu-id="fd5cc-185">API の JWT ハンドラーの構成をカスタマイズするには、その<xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>インスタンスを構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="fd5cc-186">API の JWT ハンドラーは、を使用して`JwtBearerEvents`認証プロセスを制御できるようにするイベントを発生させます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="fd5cc-187">は、API 承認のサポートを`AddIdentityServerJwt`提供するために、独自のイベントハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="fd5cc-188">イベントの処理をカスタマイズするには、必要に応じて追加のロジックを使用して既存のイベントハンドラーをラップします。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="fd5cc-189">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-189">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="fd5cc-190">前のコードでは、 `OnTokenValidated`イベントハンドラーはカスタム実装に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="fd5cc-191">この実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-191">This implementation:</span></span>

1. <span data-ttu-id="fd5cc-192">API 承認サポートによって提供される元の実装を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="fd5cc-193">独自のカスタムロジックを実行します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="fd5cc-194">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="fd5cc-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="fd5cc-195">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="fd5cc-196">例として、主要なアプリの`fetch-data`角度モジュール内でルートがどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="fd5cc-197">ルートを保護しても実際のエンドポイントが保護されないことに注意し`[Authorize]`てください (まだ属性が適用されている必要があります) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="fd5cc-198">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="fd5cc-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="fd5cc-199">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="fd5cc-200">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="fd5cc-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="fd5cc-201">プレーン`AuthorizeRoute` `Route`コンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="fd5cc-202">たとえば、 `fetch-data` `App`コンポーネント内でルートがどのように構成されているかに注目してください。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="fd5cc-203">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="fd5cc-203">Protecting a route:</span></span>

* <span data-ttu-id="fd5cc-204">は実際のエンドポイントを保護しません`[Authorize]` (まだ属性が適用されている必要があります)。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="fd5cc-205">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="fd5cc-206">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="fd5cc-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="fd5cc-207">を使用して要求を認証するには`authService` 、 `AuthorizeService`まずからインスタンスをインポートします。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="fd5cc-208">アクセストークンはから`authService`取得され、次に示すように要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="fd5cc-209">コンポーネントの反応では、通常、この作業は`componentDidMount`ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="fd5cc-210">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="fd5cc-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="fd5cc-211">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="fd5cc-211">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="fd5cc-212">運用環境への配置</span><span class="sxs-lookup"><span data-stu-id="fd5cc-212">Deploy to production</span></span>

<span data-ttu-id="fd5cc-213">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="fd5cc-214">Identityユーザーアカウントとサーバー権限を格納するデータベース。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="fd5cc-215">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="fd5cc-216">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="fd5cc-217">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="fd5cc-218">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="fd5cc-219">例: Azure Websites へのデプロイ</span><span class="sxs-lookup"><span data-stu-id="fd5cc-219">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="fd5cc-220">このセクションでは、証明書ストアに格納されている証明書を使用して、Azure websites にアプリをデプロイする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-220">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="fd5cc-221">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順でを構成するときに、App Service プランが少なくとも標準レベルになっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-221">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="fd5cc-222">アプリの*appsettings*ファイルで、 `IdentityServer`セクションを変更して、キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="fd5cc-223">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-223">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="fd5cc-224">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-224">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="fd5cc-225">ストアの場所は、証明書を読み込む場所 (`CurrentUser`また`LocalMachine`は) を表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-225">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="fd5cc-226">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-226">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="fd5cc-227">Azure Websites にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従ってアプリをデプロイし、必要な azure リソースを作成して、アプリを運用環境にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-227">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="fd5cc-228">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="fd5cc-229">アプリによって使用される証明書は、まだセットアップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-229">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="fd5cc-230">使用する証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-230">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="fd5cc-231">これらの手順では SSL について説明していますが、ポータルには [**プライベート証明書**] セクションがあります。このセクションでは、アプリで使用するプロビジョニング済みの証明書をアップロードできます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-231">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="fd5cc-232">この手順の後にアプリを再起動すると、アプリが機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-232">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="fd5cc-233">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="fd5cc-233">Other configuration options</span></span>

<span data-ttu-id="fd5cc-234">API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="fd5cc-235">言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="fd5cc-236">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="fd5cc-237">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="fd5cc-238">これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="fd5cc-239">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="fd5cc-239">Application profiles</span></span>

<span data-ttu-id="fd5cc-240">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="fd5cc-241">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="fd5cc-242">`IdentityServerSPA`: サーバーと共にホストされる SPA を1つの単位として表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="fd5cc-243">の`redirect_uri`既定値`/authentication/login-callback`はです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="fd5cc-244">の`post_logout_redirect_uri`既定値`/authentication/logout-callback`はです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="fd5cc-245">スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="fd5cc-246">許可される OIDC 応答の種類`id_token token`のセットは、それぞれ個別に`id_token`( `token`、) です。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="fd5cc-247">許可される応答モード`fragment`はです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="fd5cc-248">`SPA`: は、サーバーでホストされていない SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="fd5cc-249">スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="fd5cc-250">許可される OIDC 応答の種類`id_token token`のセットは、それぞれ個別に`id_token`( `token`、) です。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="fd5cc-251">許可される応答モード`fragment`はです。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="fd5cc-252">`IdentityServerJwt`: サービスと共にホストされる API を表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="fd5cc-253">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="fd5cc-254">`API`: は、サーバーでホストされていない API を表します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="fd5cc-255">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="fd5cc-256">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="fd5cc-256">Configuration through AppSettings</span></span>

<span data-ttu-id="fd5cc-257">構成システムを使用して、または`Clients` `Resources`の一覧にアプリを追加して、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="fd5cc-258">次の例に`redirect_uri`示す`post_logout_redirect_uri`ように、各クライアントのおよびプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="fd5cc-259">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="fd5cc-260">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="fd5cc-260">Configuration through code</span></span>

<span data-ttu-id="fd5cc-261">また、オプションを構成するためのアクションを実行するの`AddApiAuthorization`オーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="fd5cc-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="fd5cc-262">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fd5cc-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
