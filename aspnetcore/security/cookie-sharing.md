---
title: ASP.NET アプリ間で認証 cookie を共有する
author: rick-anderson
description: ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 9b5bee9fb588ef04efd50aa4a5afc3e53da1b123
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384759"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="71760-103">ASP.NET アプリ間で認証 cookie を共有する</span><span class="sxs-lookup"><span data-stu-id="71760-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="71760-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="71760-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="71760-105">多くの場合、web サイトは、連携して動作する個々の web アプリで構成されます。</span><span class="sxs-lookup"><span data-stu-id="71760-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="71760-106">シングルサインオン (SSO) エクスペリエンスを提供するには、サイト内の web アプリで認証 cookie を共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="71760-107">このシナリオをサポートするために、データ保護スタックは、Katana cookie 認証とクッキー認証チケットの ASP.NET Core 共有を許可します。</span><span class="sxs-lookup"><span data-stu-id="71760-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="71760-108">次の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71760-108">In the examples that follow:</span></span>

* <span data-ttu-id="71760-109">認証 cookie 名は、共通の`.AspNet.SharedCookie`値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="71760-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="71760-110">は`AuthenticationType` 、明示的`Identity.Application`にまたは既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="71760-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="71760-111">共通のアプリ名は、データ保護システムがデータ保護キー (`SharedCookieApp`) を共有できるようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="71760-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="71760-112">`Identity.Application`は、認証スキームとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="71760-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="71760-113">使用されるスキームにかかわらず、共有 cookie アプリ*内*でも、既定のスキームとしても、明示的に設定することによっても、一貫して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="71760-114">スキームは、cookie の暗号化と復号化に使用されるため、一貫したスキームをアプリ間で使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="71760-115">一般的な[データ保護キー](xref:security/data-protection/implementation/key-management)の保存場所が使用されます。</span><span class="sxs-lookup"><span data-stu-id="71760-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="71760-116">ASP.NET Core アプリでは<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 、を使用してキーの格納場所を設定します。</span><span class="sxs-lookup"><span data-stu-id="71760-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="71760-117">.NET Framework アプリでは、Cookie 認証ミドルウェアはの<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="71760-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="71760-118">`DataProtectionProvider`認証 cookie ペイロードデータの暗号化と復号化のためのデータ保護サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="71760-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="71760-119">`DataProtectionProvider`インスタンスは、アプリの他の部分で使用されるデータ保護システムから分離されています。</span><span class="sxs-lookup"><span data-stu-id="71760-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="71760-120">[Dataprotectionprovider. Create (DirectoryInfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)では、を<xref:System.IO.DirectoryInfo>使用して、データ保護キーの格納場所を指定できます。</span><span class="sxs-lookup"><span data-stu-id="71760-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="71760-121">`DataProtectionProvider`には、 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="71760-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="71760-122">ASP.NET Core 2.x アプリで、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照します。</span><span class="sxs-lookup"><span data-stu-id="71760-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="71760-123">.NET Framework アプリで、 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="71760-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="71760-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>共通アプリ名を設定します。</span><span class="sxs-lookup"><span data-stu-id="71760-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="71760-125">認証 cookie を ASP.NET Core Id と共有する</span><span class="sxs-lookup"><span data-stu-id="71760-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="71760-126">ASP.NET Core Id を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="71760-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="71760-127">データ保護キーとアプリ名は、アプリ間で共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="71760-128">共通のキー格納場所は、次の<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>例のメソッドに提供されます。</span><span class="sxs-lookup"><span data-stu-id="71760-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="71760-129">共通<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>の共有アプリ名を構成するに`SharedCookieApp`は、を使用します (次の例を参考にしてください)。</span><span class="sxs-lookup"><span data-stu-id="71760-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="71760-130">詳細については、「 <xref:security/data-protection/configuration/overview> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71760-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="71760-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>拡張メソッドを使用して、cookie 用のデータ保護サービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="71760-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="71760-132">既定の認証の種類`Identity.Application`はです。</span><span class="sxs-lookup"><span data-stu-id="71760-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="71760-133">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="71760-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="71760-134">ASP.NET Core Id なしで認証 cookie を共有する</span><span class="sxs-lookup"><span data-stu-id="71760-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="71760-135">ASP.NET Core Id なしで cookie を直接使用する場合は、で`Startup.ConfigureServices`データ保護と認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="71760-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71760-136">次の例では、認証の種類がに`Identity.Application`設定されています。</span><span class="sxs-lookup"><span data-stu-id="71760-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="71760-137">異なるベースパス間での cookie の共有</span><span class="sxs-lookup"><span data-stu-id="71760-137">Share cookies across different base paths</span></span>

<span data-ttu-id="71760-138">認証クッキーは、既定の[cookie のパス](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)として[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)を使用します。</span><span class="sxs-lookup"><span data-stu-id="71760-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="71760-139">アプリの cookie を異なるベースパス間で共有する必要が`Path`ある場合は、をオーバーライドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="71760-140">サブドメイン間での cookie の共有</span><span class="sxs-lookup"><span data-stu-id="71760-140">Share cookies across subdomains</span></span>

<span data-ttu-id="71760-141">サブドメイン間で cookie を共有するアプリをホストする場合は、" [cookie. ドメイン](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)" プロパティで共通のドメインを指定します。</span><span class="sxs-lookup"><span data-stu-id="71760-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="71760-142">`first_subdomain.contoso.com`や`contoso.com` `Cookie.Domain` `.contoso.com`などのアプリ間で cookie を共有するには、をとして指定します。 `second_subdomain.contoso.com`</span><span class="sxs-lookup"><span data-stu-id="71760-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="71760-143">保存時のデータ保護キーの暗号化</span><span class="sxs-lookup"><span data-stu-id="71760-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="71760-144">運用環境のデプロイの場合`DataProtectionProvider`は、DPAPI または X509Certificate を使用して保存時のキーを暗号化するようにを構成します。</span><span class="sxs-lookup"><span data-stu-id="71760-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="71760-145">詳細については、「 <xref:security/data-protection/implementation/key-encryption-at-rest> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71760-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="71760-146">次の例では、証明書の拇印が<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>に提供されています。</span><span class="sxs-lookup"><span data-stu-id="71760-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="71760-147">ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する</span><span class="sxs-lookup"><span data-stu-id="71760-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="71760-148">Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリは、ASP.NET Core Cookie 認証ミドルウェアと互換性のある認証 cookie を生成するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="71760-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="71760-149">これにより、複数の手順で大規模なサイトの個々のアプリをアップグレードしながら、サイト全体でスムーズに SSO を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="71760-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="71760-150">アプリで Katana Cookie 認証ミドルウェアを使用すると、 `UseCookieAuthentication`プロジェクトの*Startup.Auth.cs*ファイルでが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="71760-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="71760-151">Visual Studio 2013 以降で作成された ASP.NET 4.x web アプリプロジェクトは、既定で Katana Cookie 認証ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="71760-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="71760-152">は互換性のために残されており、 `UseCookieAuthentication` ASP.NET Core アプリではサポートされていませんが、Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリでを呼び出すことは有効です。 `UseCookieAuthentication`</span><span class="sxs-lookup"><span data-stu-id="71760-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="71760-153">ASP.NET 4.x アプリは .NET Framework 4.5.1 以降を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="71760-154">そうしないと、必要な NuGet パッケージのインストールに失敗します。</span><span class="sxs-lookup"><span data-stu-id="71760-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="71760-155">ASP.NET 4.x アプリと ASP.NET Core アプリの間で認証 cookie を共有するには、「 [ASP.NET Core アプリ間で認証 cookie を共有](#share-authentication-cookies-with-aspnet-core-identity)する」セクションの説明に従って ASP.NET Core アプリを構成し、次のように ASP.NET 4.x アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="71760-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="71760-156">アプリのパッケージが最新リリースに更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="71760-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="71760-157">それぞれの ASP.NET 4.x アプリに[Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="71760-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="71760-158">の呼び出しを`UseCookieAuthentication`見つけて変更します。</span><span class="sxs-lookup"><span data-stu-id="71760-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="71760-159">Cookie 名を、ASP.NET Core cookie 認証ミドルウェア (`.AspNet.SharedCookie`例では) で使用されている名前と一致するように変更します。</span><span class="sxs-lookup"><span data-stu-id="71760-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="71760-160">次の例では、認証の種類がに`Identity.Application`設定されています。</span><span class="sxs-lookup"><span data-stu-id="71760-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="71760-161">Common data protection キーの`DataProtectionProvider`格納場所に初期化されたのインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="71760-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="71760-162">アプリケーション名が、認証 cookie を共有するすべてのアプリ (`SharedCookieApp`例では) によって使用される共通アプリ名に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="71760-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="71760-163">およびを設定`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`し`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`てい<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>ない場合は、一意のユーザーを識別するクレームをに設定します。</span><span class="sxs-lookup"><span data-stu-id="71760-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="71760-164">*App_Start/Startup*:</span><span class="sxs-lookup"><span data-stu-id="71760-164">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="71760-165">ユーザー id を生成するときは、認証の`Identity.Application`種類 () が、 *App_Start/Startup* `UseCookieAuthentication`ので`AuthenticationType`設定されたで定義されている型と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="71760-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="71760-166">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="71760-166">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="71760-167">共通のユーザーデータベースを使用する</span><span class="sxs-lookup"><span data-stu-id="71760-167">Use a common user database</span></span>

<span data-ttu-id="71760-168">アプリで同じ Id スキーマ (同じバージョンの Id) を使用する場合は、各アプリの Id システムが同じユーザーデータベースで参照されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="71760-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="71760-169">それ以外の場合、id システムは、認証クッキーの情報とデータベース内の情報を照合しようとしたときに、実行時にエラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="71760-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="71760-170">アプリ間で Id スキーマが異なる場合、通常、アプリでは異なる Id バージョンが使用されるため、最新バージョンの Id に基づいて共通データベースを共有することはできません。他のアプリの Id スキーマに再マップして列を追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="71760-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="71760-171">多くの場合、アプリで共通のデータベースを共有できるように、他のアプリをアップグレードして最新の Id バージョンを使用する方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="71760-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71760-172">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="71760-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
