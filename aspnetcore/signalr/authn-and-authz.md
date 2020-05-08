---
title: ASP.NET Core での認証と承認SignalR
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 0f0bb2040d2407817c91f64a4769e6601c37a07d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775285"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="fa39b-103">ASP.NET Core での認証と承認SignalR</span><span class="sxs-lookup"><span data-stu-id="fa39b-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="fa39b-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="fa39b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="fa39b-105">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa39b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="fa39b-106">SignalRハブに接続しているユーザーを認証する</span><span class="sxs-lookup"><span data-stu-id="fa39b-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="fa39b-107">[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用して、ユーザーを各接続に関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="fa39b-108">ハブでは、 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティから認証データにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="fa39b-109">認証を使用すると、ユーザーに関連付けられているすべての接続で、ハブがメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="fa39b-110">詳細については、「 [」のSignalR「ユーザーとグループの管理](xref:signalr/groups)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa39b-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="fa39b-111">複数の接続を1人のユーザーに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="fa39b-112">認証を使用して ASP.NET Core `Startup.Configure`認証をSignalR使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="fa39b-113">SignalRおよび ASP.NET Core 認証ミドルウェアを登録する順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="fa39b-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="fa39b-114">がの`UseAuthentication`ユーザー `UseSignalR`を持つSignalRようにするには`HttpContext`、常にを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="fa39b-115">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="fa39b-115">Cookie authentication</span></span>

<span data-ttu-id="fa39b-116">ブラウザーベースのアプリでは、cookie 認証によって、既存のユーザーの資格SignalR情報を自動的に接続に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="fa39b-117">ブラウザークライアントを使用する場合、追加の構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="fa39b-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="fa39b-118">ユーザーがアプリにログインしている場合、接続SignalRは自動的にこの認証を継承します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="fa39b-119">Cookie はアクセストークンを送信するためのブラウザー固有の方法ですが、ブラウザー以外のクライアントは送信できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="fa39b-120">[.Net クライアント](xref:signalr/dotnet-client)を使用する場合、 `Cookies`プロパティを`.WithUrl`呼び出しでクッキーを指定するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="fa39b-121">ただし、.NET クライアントから cookie 認証を使用するには、アプリが cookie の認証データを交換するための API を提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="fa39b-122">ベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="fa39b-122">Bearer token authentication</span></span>

<span data-ttu-id="fa39b-123">クライアントは、cookie を使用する代わりにアクセストークンを提供できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="fa39b-124">サーバーはトークンを検証し、それを使用してユーザーを識別します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="fa39b-125">この検証は、接続が確立された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="fa39b-126">接続の有効期間中は、トークンの失効を確認するためにサーバーが自動的に再検証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="fa39b-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="fa39b-127">サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="fa39b-128">JavaScript クライアントでは、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプションを使用してトークンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="fa39b-129">.NET クライアントには、トークンの構成に使用できる同様の[れる場合](xref:signalr/configuration#configure-bearer-authentication)プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fa39b-130">提供するアクセストークン関数は、によっ**every**てSignalR行われるすべての HTTP 要求の前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="fa39b-131">接続をアクティブな状態に保つために、トークンを更新する必要がある場合 (接続中に期限切れになる可能性があるため)、この関数内からトークンを取得し、更新されたトークンを返します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="fa39b-132">標準の web Api では、ベアラートークンは HTTP ヘッダーで送信されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="fa39b-133">ただし、 SignalRでは、一部のトランスポートを使用するときに、これらのヘッダーをブラウザーで設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="fa39b-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="fa39b-134">Websocket およびサーバー送信イベントを使用する場合、トークンはクエリ文字列パラメーターとして送信されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="fa39b-135">サーバーでこれをサポートするには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="fa39b-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="fa39b-136">クエリ文字列は、ブラウザー API の制限により、Websocket およびサーバーから送信されたイベントと接続するときにブラウザーで使用されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="fa39b-137">HTTPS を使用する場合、クエリ文字列の値は TLS 接続によって保護されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="fa39b-138">ただし、多くのサーバーはクエリ文字列の値をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-138">However, many servers log query string values.</span></span> <span data-ttu-id="fa39b-139">詳細については、「 [ASP.NET Core SignalRのセキュリティに関する考慮事項](xref:signalr/security)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa39b-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="fa39b-140">はヘッダーを使用して、トークンをサポートする環境 (.NET や Java クライアントなど) でトークンを送信します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="fa39b-141">Cookie とベアラートークン</span><span class="sxs-lookup"><span data-stu-id="fa39b-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="fa39b-142">Cookie は、ブラウザーに固有のものです。</span><span class="sxs-lookup"><span data-stu-id="fa39b-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="fa39b-143">他の種類のクライアントから送信すると、ベアラートークンの送信と比較して複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="fa39b-144">そのため、アプリがブラウザークライアントからのユーザーの認証のみを必要とする場合を除き、cookie 認証は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="fa39b-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="fa39b-145">ベアラートークン認証は、ブラウザークライアント以外のクライアントを使用する場合に推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="fa39b-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="fa39b-146">[Windows 認証]</span><span class="sxs-lookup"><span data-stu-id="fa39b-146">Windows authentication</span></span>

<span data-ttu-id="fa39b-147">アプリで[Windows 認証](xref:security/authentication/windowsauth)が構成されてSignalRいる場合、はその id を使用してハブをセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="fa39b-148">ただし、個々のユーザーにメッセージを送信するには、カスタムユーザー ID プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="fa39b-149">Windows 認証システムは、"名前識別子" 要求を提供しません。</span><span class="sxs-lookup"><span data-stu-id="fa39b-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="fa39b-150">は、要求を使用してユーザー名を決定します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="fa39b-151">を実装`IUserIdProvider`し、識別子として使用する要求の1つをユーザーから取得する新しいクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="fa39b-152">たとえば、"Name" 要求 (フォーム`[Domain]\[Username]`の Windows ユーザー名) を使用するには、次のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="fa39b-153">`ClaimTypes.Name`ではなく、の`User`任意の値 (Windows SID 識別子など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="fa39b-154">選択する値は、システム内のすべてのユーザーの間で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="fa39b-155">そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="fa39b-156">`Startup.ConfigureServices`メソッドにこのコンポーネントを登録します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="fa39b-157">.NET クライアントでは、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)プロパティを設定することによって Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="fa39b-158">Windows 認証は、Microsoft Internet Explorer または Microsoft Edge を使用している場合にのみブラウザークライアントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="fa39b-159">要求を使用して id 処理をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="fa39b-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="fa39b-160">ユーザーを認証するアプリは、 SignalRユーザー要求からユーザー id を派生させることができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="fa39b-161">がユーザー Id SignalRを作成する方法を`IUserIdProvider`指定するには、実装を実装して登録します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="fa39b-162">このサンプルコードでは、要求を使用して、識別プロパティとしてユーザーの電子メールアドレスを選択する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="fa39b-163">選択する値は、システム内のすべてのユーザーの間で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="fa39b-164">そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="fa39b-165">アカウント登録によって、種類`ClaimsTypes.Email`がの要求が ASP.NET identity データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="fa39b-166">このコンポーネントをに登録`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="fa39b-167">ハブおよびハブのメソッドへのアクセスをユーザーに承認する</span><span class="sxs-lookup"><span data-stu-id="fa39b-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="fa39b-168">既定では、ハブ内のすべてのメソッドを認証されていないユーザーが呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="fa39b-169">認証を要求するには、[承認](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性をハブに適用します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="fa39b-170">`[Authorize]`属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="fa39b-171">たとえば、という`MyAuthorizationPolicy`カスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがハブにアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="fa39b-172">個々のハブメソッドに`[Authorize]`も属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="fa39b-173">現在のユーザーがメソッドに適用されているポリシーと一致しない場合は、呼び出し元にエラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="fa39b-174">承認ハンドラーを使用してハブメソッドの承認をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="fa39b-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="fa39b-175">ハブメソッドが承認を必要とする場合に、カスタムリソースを承認ハンドラーに提供します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="fa39b-176">リソースは `HubInvocationContext` のインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="fa39b-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="fa39b-177">に`HubInvocationContext`は、 `HubCallerContext`、呼び出されるハブメソッドの名前、およびハブメソッドへの引数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="fa39b-178">Azure Active Directory による複数の組織でのサインインを可能にするチャットルームの例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="fa39b-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="fa39b-179">Microsoft アカウントを持つユーザーはだれでもチャットにサインインできますが、所有している組織のメンバーだけがユーザーの許可を禁止したり、ユーザーのチャット履歴を表示したりできるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fa39b-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="fa39b-180">さらに、特定のユーザーの特定の機能を制限することもできます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="fa39b-181">ASP.NET Core 3.0 の更新された機能を使用すると、これは完全に可能です。</span><span class="sxs-lookup"><span data-stu-id="fa39b-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="fa39b-182">が`DomainRestrictedRequirement`カスタム`IAuthorizationRequirement`として機能することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fa39b-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="fa39b-183">`HubInvocationContext`リソースパラメーターが渡されたので、内部ロジックはハブが呼び出されているコンテキストを検査し、ユーザーが個々のハブメソッドを実行できるようにすることを決定します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="fa39b-184">で`Startup.ConfigureServices`、新しいポリシーを追加し、 `DomainRestrictedRequirement` `DomainRestricted`ポリシーを作成するためのパラメーターとしてカスタム要件を指定します。</span><span class="sxs-lookup"><span data-stu-id="fa39b-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="fa39b-185">前の例では、 `DomainRestrictedRequirement`クラスは`IAuthorizationRequirement`との両方で、 `AuthorizationHandler`その要件に対応しています。</span><span class="sxs-lookup"><span data-stu-id="fa39b-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="fa39b-186">この2つのコンポーネントを別々のクラスに分割して、懸念事項を分けることができます。</span><span class="sxs-lookup"><span data-stu-id="fa39b-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="fa39b-187">この例のアプローチの利点は、要件とハンドラーが同じである`AuthorizationHandler`ため、起動時にを挿入する必要がないことです。</span><span class="sxs-lookup"><span data-stu-id="fa39b-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fa39b-188">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fa39b-188">Additional resources</span></span>

* [<span data-ttu-id="fa39b-189">ASP.NET Core でのベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="fa39b-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="fa39b-190">リソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="fa39b-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
