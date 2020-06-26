---
title: Microsoft アカウントを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 7114cf60639c7fdff9fb9d5713dd524f3cf27a96
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103386"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="b0904-102">Microsoft アカウントを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="b0904-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="b0904-103">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0904-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b0904-104">認証用に [Azure Active Directory (AAD) を使用する Microsoft アカウント](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する Blazor WebAssembly スタンドアロン アプリを作成する方法:</span><span class="sxs-lookup"><span data-stu-id="b0904-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="b0904-105">AAD テナントと Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="b0904-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="b0904-106">Azure portal の **[Azure Active Directory]**  >  **[アプリの登録]** 領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="b0904-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b0904-107">アプリの**名前**を指定します (例: **Blazor スタンドアロン AAD Microsoft アカウント**)。</span><span class="sxs-lookup"><span data-stu-id="b0904-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="b0904-108">**[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b0904-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="b0904-109">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="b0904-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b0904-110">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="b0904-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b0904-111">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="b0904-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b0904-112">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="b0904-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b0904-113">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="b0904-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b0904-114">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b0904-115">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="b0904-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b0904-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b0904-116">Select **Register**.</span></span>

<span data-ttu-id="b0904-117">アプリケーション ID (クライアント ID) を記録しておきます (例: `11111111-1111-1111-1111-111111111111`)。</span><span class="sxs-lookup"><span data-stu-id="b0904-117">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="b0904-118">**[認証]**  >  **[プラットフォーム構成]**  >  **[Web]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="b0904-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b0904-119">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b0904-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b0904-120">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="b0904-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b0904-121">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="b0904-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b0904-122">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b0904-122">Select the **Save** button.</span></span>

<span data-ttu-id="b0904-123">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b0904-123">Create the app.</span></span> <span data-ttu-id="b0904-124">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b0904-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="b0904-125">出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b0904-126">また、フォルダー名は、プロジェクトの名前の一部となります。</span><span class="sxs-lookup"><span data-stu-id="b0904-126">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b0904-127">Azure portal では、アプリの **[認証]**  >  **[プラットフォーム構成]**  >  **[Web]**  >  **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-127">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b0904-128">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="b0904-128">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b0904-129">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="b0904-129">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="b0904-130">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="b0904-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="b0904-131">Microsoft アカウントを使用してアプリにログインする。</span><span class="sxs-lookup"><span data-stu-id="b0904-131">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="b0904-132">Microsoft API のアクセス トークンを要求する。</span><span class="sxs-lookup"><span data-stu-id="b0904-132">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="b0904-133">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="b0904-133">For more information, see:</span></span>
  * [<span data-ttu-id="b0904-134">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="b0904-134">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="b0904-135">[クイック スタート:Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0904-135">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b0904-136">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="b0904-136">Authentication package</span></span>

<span data-ttu-id="b0904-137">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b0904-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b0904-138">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="b0904-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b0904-139">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="b0904-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b0904-140">[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって、[Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-140">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b0904-141">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="b0904-141">Authentication service support</span></span>

<span data-ttu-id="b0904-142">ユーザーの認証に対するサポートは、[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-142">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b0904-143">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なすべてのサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-143">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b0904-144">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0904-144">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="b0904-145"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="b0904-145">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b0904-146">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="b0904-146">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="b0904-147">構成は、*wwwroot/appsettings.json* ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b0904-147">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b0904-148">例:</span><span class="sxs-lookup"><span data-stu-id="b0904-148">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="b0904-149">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="b0904-149">Access token scopes</span></span>

<span data-ttu-id="b0904-150">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="b0904-150">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b0904-151">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の既定のアクセス トークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="b0904-151">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b0904-152">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0904-152">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b0904-153">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="b0904-153">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b0904-154">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="b0904-154">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b0904-155">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="b0904-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b0904-156">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="b0904-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b0904-157">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b0904-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b0904-158">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b0904-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b0904-159">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b0904-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b0904-160">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b0904-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b0904-161">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b0904-161">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="b0904-162">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="b0904-162">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="b0904-163">クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="b0904-163">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="b0904-164">クイック スタート:Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="b0904-164">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)