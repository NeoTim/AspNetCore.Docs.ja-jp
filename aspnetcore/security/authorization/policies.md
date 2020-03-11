---
title: ASP.NET Core でのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を強制するために承認ポリシーハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: eeb5ddd63ef8177325b35e5a666aa5e9ab047057
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652976"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core でのポリシーベースの承認

::: moniker range=">= aspnetcore-3.0"

内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。 これらの構成要素は、コードでの承認評価の式をサポートします。 結果として、より充実した再利用可能な承認の構造が得られます。

承認ポリシーは、1つまたは複数の要件で構成されます。 認証サービス構成の一部として、`Startup.ConfigureServices` メソッドに登録されます。

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

前の例では、"AtLeast21" ポリシーが作成されています。 これには、要件にパラメーターとして指定されている最小有効期間の1つの要件&mdash;があります。

## <a name="iauthorizationservice"></a>IAuthorizationService 

承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>次のとおりです。

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> は、メソッドのないマーカーサービスと、承認が成功したかどうかを追跡するための機構です。

各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> クラスは、要件が満たされているかどうかをマークするためにハンドラーが使用するものです。

```csharp
 context.Succeed(requirement)
```

次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

次のコードは、一般的な `ConfigureServices`を示しています。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

承認には <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> または `[Authorize(Policy = "Something")]` を使用します。

## <a name="applying-policies-to-mvc-controllers"></a>MVC コントローラーへのポリシーの適用

Razor Pages を使用している場合は、このドキュメントの「 [Razor Pages にポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。

ポリシーは、ポリシー名と共に `[Authorize]` 属性を使用して、コントローラーに適用されます。 例 :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor Pages にポリシーを適用する

ポリシーは、`[Authorize]` 属性をポリシー名と共に使用して、Razor Pages に適用されます。 例 :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor Pages に適用することもできます。

## <a name="requirements"></a>要件

承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。 "AtLeast21" ポリシーでは、最小有効期間を&mdash;1 つのパラメーターが必要です。 要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。 パラメーター化された最小年齢要件は、次のように実装できます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。 つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。

> [!NOTE]
> 要件には、データまたはプロパティを含める必要はありません。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>承認ハンドラー

承認ハンドラーは、要件のプロパティを評価する役割を担います。 承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。

要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。 ハンドラーは、 [Authorizationhandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 `TRequirement` は処理する必要があります。 また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。

### <a name="use-a-handler-for-one-requirement"></a>1つの要件に対してハンドラーを使用する

<a name="security-authorization-handler-example"></a>

次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。 要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。 クレームが存在する場合は、ユーザーの年齢が計算されます。 ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。 承認が成功すると、その唯一のパラメーターとして、満たされた要件を使用して `context.Succeed` が呼び出されます。

### <a name="use-a-handler-for-multiple-requirements"></a>複数の要件に対してハンドラーを使用する

次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上記のコードは、成功とマークされていない要件を含むプロパティ&mdash;、 [Pendingrequirements 要件](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)を走査します。 `ReadPermission` の要件では、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。 `EditPermission` または `DeletePermission` 要件の場合は、要求されたリソースにアクセスするための所有者である必要があります。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>ハンドラーの登録

ハンドラーは、構成中にサービスコレクションに登録されます。 例 :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

上記のコードでは、`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`を呼び出すことによって `MinimumAgeHandler` をシングルトンとして登録します。 ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。

## <a name="what-should-a-handler-return"></a>ハンドラーが返すもの

[ハンドラーの例](#security-authorization-handler-example)の `Handle` メソッドは値を返さないことに注意してください。 成功または失敗のいずれかの状態はどのように示されますか。

* ハンドラーは `context.Succeed(IAuthorizationRequirement requirement)`を呼び出して成功を示し、正常に検証された要件を渡します。

* 同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。

* エラーを保証するには、他の要件ハンドラーが成功した場合でも、`context.Fail`を呼び出します。

ハンドラーが `context.Succeed` または `context.Fail`を呼び出す場合でも、他のすべてのハンドラーが呼び出されます。 これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。 `false`に設定すると、`context.Fail` が呼び出されたときにハンドラーの実行をショートサーキットする[Invokeハンドラの Terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用できます) が表示されます。 `InvokeHandlersAfterFailure` 既定値は `true`です。この場合、すべてのハンドラーが呼び出されます。

> [!NOTE]
> 認証ハンドラーは、認証が失敗した場合でも呼び出されます。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>1つの要件に対して複数のハンドラーが必要なのはなぜですか。

評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。 たとえば、Microsoft には、キーカードだけを開くドアがあります。 キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。 このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。 ポリシーによって `BuildingEntryRequirement`が評価されたときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。

## <a name="using-a-func-to-fulfill-a-policy"></a>Func を使用してポリシーを満たす

ポリシーを実現することは、コード内で簡単に行うことができます。 `RequireAssertion` ポリシービルダーを使用してポリシーを構成するときに、`Func<AuthorizationHandlerContext, bool>` を指定できます。

たとえば、前の `BadgeEntryHandler` は次のように書き換えることができます。

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>ハンドラーでの MVC 要求コンテキストへのアクセス

承認ハンドラーに実装する `HandleRequirementAsync` メソッドには、`AuthorizationHandlerContext` と処理する `TRequirement` の2つのパラメーターがあります。 MVC や Jabbr などのフレームワークは、`AuthorizationHandlerContext` の `Resource` プロパティに任意のオブジェクトを追加して、追加情報を渡すことができます。

たとえば、MVC は、`Resource` プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡します。 このプロパティは、MVC および Razor Pages によって提供される `HttpContext`、`RouteData`、およびその他のすべてへのアクセスを提供します。

`Resource` プロパティの使用は、フレームワーク固有です。 `Resource` プロパティの情報を使用すると、承認ポリシーが特定のフレームワークに限定されます。 `is` キーワードを使用して `Resource` プロパティをキャストし、キャストが成功したことを確認して、他のフレームワークで実行したときにコードが `InvalidCastException` でクラッシュしないようにします。

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。 これらの構成要素は、コードでの承認評価の式をサポートします。 結果として、より充実した再利用可能な承認の構造が得られます。

承認ポリシーは、1つまたは複数の要件で構成されます。 認証サービス構成の一部として、`Startup.ConfigureServices` メソッドに登録されます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

前の例では、"AtLeast21" ポリシーが作成されています。 これには、要件にパラメーターとして指定されている最小有効期間の1つの要件&mdash;があります。

## <a name="iauthorizationservice"></a>IAuthorizationService 

承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>次のとおりです。

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> は、メソッドのないマーカーサービスと、承認が成功したかどうかを追跡するための機構です。

各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> クラスは、要件が満たされているかどうかをマークするためにハンドラーが使用するものです。

```csharp
 context.Succeed(requirement)
```

次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

次のコードは、一般的な `ConfigureServices`を示しています。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

承認には <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> または `[Authorize(Policy = "Something")]` を使用します。

## <a name="applying-policies-to-mvc-controllers"></a>MVC コントローラーへのポリシーの適用

Razor Pages を使用している場合は、このドキュメントの「 [Razor Pages にポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。

ポリシーは、ポリシー名と共に `[Authorize]` 属性を使用して、コントローラーに適用されます。 例 :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor Pages にポリシーを適用する

ポリシーは、`[Authorize]` 属性をポリシー名と共に使用して、Razor Pages に適用されます。 例 :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor Pages に適用することもできます。

## <a name="requirements"></a>要件

承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。 "AtLeast21" ポリシーでは、最小有効期間を&mdash;1 つのパラメーターが必要です。 要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。 パラメーター化された最小年齢要件は、次のように実装できます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。 つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。

> [!NOTE]
> 要件には、データまたはプロパティを含める必要はありません。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>承認ハンドラー

承認ハンドラーは、要件のプロパティを評価する役割を担います。 承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。

要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。 ハンドラーは、 [Authorizationhandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 `TRequirement` は処理する必要があります。 また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。

### <a name="use-a-handler-for-one-requirement"></a>1つの要件に対してハンドラーを使用する

<a name="security-authorization-handler-example"></a>

次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。 要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。 クレームが存在する場合は、ユーザーの年齢が計算されます。 ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。 承認が成功すると、その唯一のパラメーターとして、満たされた要件を使用して `context.Succeed` が呼び出されます。

### <a name="use-a-handler-for-multiple-requirements"></a>複数の要件に対してハンドラーを使用する

次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上記のコードは、成功とマークされていない要件を含むプロパティ&mdash;、 [Pendingrequirements 要件](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)を走査します。 `ReadPermission` の要件では、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。 `EditPermission` または `DeletePermission` 要件の場合は、要求されたリソースにアクセスするための所有者である必要があります。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>ハンドラーの登録

ハンドラーは、構成中にサービスコレクションに登録されます。 例 :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

上記のコードでは、`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`を呼び出すことによって `MinimumAgeHandler` をシングルトンとして登録します。 ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。

## <a name="what-should-a-handler-return"></a>ハンドラーが返すもの

[ハンドラーの例](#security-authorization-handler-example)の `Handle` メソッドは値を返さないことに注意してください。 成功または失敗のいずれかの状態はどのように示されますか。

* ハンドラーは `context.Succeed(IAuthorizationRequirement requirement)`を呼び出して成功を示し、正常に検証された要件を渡します。

* 同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。

* エラーを保証するには、他の要件ハンドラーが成功した場合でも、`context.Fail`を呼び出します。

ハンドラーが `context.Succeed` または `context.Fail`を呼び出す場合でも、他のすべてのハンドラーが呼び出されます。 これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。 `false`に設定すると、`context.Fail` が呼び出されたときにハンドラーの実行をショートサーキットする[Invokeハンドラの Terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用できます) が表示されます。 `InvokeHandlersAfterFailure` 既定値は `true`です。この場合、すべてのハンドラーが呼び出されます。

> [!NOTE]
> 認証ハンドラーは、認証が失敗した場合でも呼び出されます。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>1つの要件に対して複数のハンドラーが必要なのはなぜですか。

評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。 たとえば、Microsoft には、キーカードだけを開くドアがあります。 キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。 このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。 ポリシーによって `BuildingEntryRequirement`が評価されたときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。

## <a name="using-a-func-to-fulfill-a-policy"></a>Func を使用してポリシーを満たす

ポリシーを実現することは、コード内で簡単に行うことができます。 `RequireAssertion` ポリシービルダーを使用してポリシーを構成するときに、`Func<AuthorizationHandlerContext, bool>` を指定できます。

たとえば、前の `BadgeEntryHandler` は次のように書き換えることができます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>ハンドラーでの MVC 要求コンテキストへのアクセス

承認ハンドラーに実装する `HandleRequirementAsync` メソッドには、`AuthorizationHandlerContext` と処理する `TRequirement` の2つのパラメーターがあります。 MVC や Jabbr などのフレームワークは、`AuthorizationHandlerContext` の `Resource` プロパティに任意のオブジェクトを追加して、追加情報を渡すことができます。

たとえば、MVC は、`Resource` プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡します。 このプロパティは、MVC および Razor Pages によって提供される `HttpContext`、`RouteData`、およびその他のすべてへのアクセスを提供します。

`Resource` プロパティの使用は、フレームワーク固有です。 `Resource` プロパティの情報を使用すると、承認ポリシーが特定のフレームワークに限定されます。 `is` キーワードを使用して `Resource` プロパティをキャストし、キャストが成功したことを確認して、他のフレームワークで実行したときにコードが `InvalidCastException` でクラッシュしないようにします。

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end