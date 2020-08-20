---
title: ASP.NET から ASP.NET Core への移行
author: isaac2004
description: 既存の ASP.NET MVC または Web API アプリを ASP.NET Core.web に移行するときのガイダンスをご覧ください
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7f5d2835d93631ac73b3da0c3dc26d87ef64c57d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634762"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="b5385-103">ASP.NET から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="b5385-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="b5385-104">著者: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="b5385-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="b5385-105">この記事は、ASP.NET アプリを ASP.NET Core に移行するための参考ガイドです。</span><span class="sxs-lookup"><span data-stu-id="b5385-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5385-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b5385-106">Prerequisites</span></span>

[<span data-ttu-id="b5385-107">.NET Core SDK 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="b5385-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="b5385-108">ターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="b5385-108">Target frameworks</span></span>

<span data-ttu-id="b5385-109">ASP.NET Core プロジェクトを使うと、開発者は、.NET Core と .NET Framework のどちらか一方または両方を対象にして柔軟に開発できます。</span><span class="sxs-lookup"><span data-stu-id="b5385-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="b5385-110">最も適切なターゲット フレームワークの決定については、「[サーバー アプリ用 .NET Core と .NET Framework の選択](/dotnet/standard/choosing-core-framework-server)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b5385-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="b5385-111">.NET Framework を対象にする場合は、プロジェクトで個々の NuGet パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5385-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="b5385-112">.NET Core を対象にすると、ASP.NET Core [メタパッケージ](xref:fundamentals/metapackage-app)のおかげで、さまざまな明示的パッケージ参照をしなくて済みます。</span><span class="sxs-lookup"><span data-stu-id="b5385-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b5385-113">`Microsoft.AspNetCore.App` メタパッケージをプロジェクトにインストールします。</span><span class="sxs-lookup"><span data-stu-id="b5385-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="b5385-114">メタパッケージを使うと、メタパッケージ内で参照されているパッケージはアプリでは展開されません。</span><span class="sxs-lookup"><span data-stu-id="b5385-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="b5385-115">.NET Core ランタイム ストアにはこれらのアセットが含まれており、パフォーマンス向上のためにプリコンパイルされています。</span><span class="sxs-lookup"><span data-stu-id="b5385-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="b5385-116">詳細については、[ASP.NET Core 用の Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5385-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="b5385-117">プロジェクトの構造の違い</span><span class="sxs-lookup"><span data-stu-id="b5385-117">Project structure differences</span></span>

<span data-ttu-id="b5385-118">*.csproj* ファイルの形式は、ASP.NET Core では簡素化されています。</span><span class="sxs-lookup"><span data-stu-id="b5385-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="b5385-119">いくつかの重要な変更は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b5385-119">Some notable changes include:</span></span>

- <span data-ttu-id="b5385-120">ファイルがプロジェクトの一部と見なされるためにファイルを明示的に含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b5385-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="b5385-121">これにより、大規模なチームで作業する場合に XML のマージが競合するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="b5385-122">他のプロジェクトを GUID で参照することはなくなり、ファイルの読みやすさが向上します。</span><span class="sxs-lookup"><span data-stu-id="b5385-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="b5385-123">Visual Studio でアンロードせずにファイルを編集することができます。</span><span class="sxs-lookup"><span data-stu-id="b5385-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017 の CSPROJ の編集コンテキスト メニュー オプション](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="b5385-125">Global.asax ファイルの置換</span><span class="sxs-lookup"><span data-stu-id="b5385-125">Global.asax file replacement</span></span>

<span data-ttu-id="b5385-126">ASP.NET Core では、アプリをブートストラップする新しいメカニズムが導入されました。</span><span class="sxs-lookup"><span data-stu-id="b5385-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="b5385-127">ASP.NET アプリケーションのエントリ ポイントは、*Global.asax* ファイルです。</span><span class="sxs-lookup"><span data-stu-id="b5385-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="b5385-128">ルート構成、フィルター、領域の登録などのタスクは、*Global.asax* ファイルで処理されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="b5385-129">このアプローチでは、アプリケーションとその展開先のサーバーが、実装を妨げるような方法で結合されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="b5385-130">結合を切り離すため、複数のフレームワークを一緒に使うさらにクリーンな方法を提供する [OWIN](https://owin.org/) が導入されました。</span><span class="sxs-lookup"><span data-stu-id="b5385-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="b5385-131">OWIN は、必要なモジュールのみを追加するためのパイプラインを提供します。</span><span class="sxs-lookup"><span data-stu-id="b5385-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="b5385-132">ホスティング環境は、[Startup](xref:fundamentals/startup) 関数を取得して、サービスとアプリの要求パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="b5385-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="b5385-133">`Startup` は、ミドルウェアのセットをアプリケーションに登録します。</span><span class="sxs-lookup"><span data-stu-id="b5385-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="b5385-134">アプリケーションは、要求ごとに、既存のハンドラーのセットに対するリンク リストのヘッド ポインターを指定して、各ミドルウェア コンポーネントを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b5385-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="b5385-135">各ミドルウェア コンポーネントは、要求処理パイプラインに 1 つ以上のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b5385-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="b5385-136">これは、新しいリストのヘッドであるハンドラーへの参照を返すことによって行われます。</span><span class="sxs-lookup"><span data-stu-id="b5385-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="b5385-137">各ハンドラーは、リスト内の次のハンドラーを記憶して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b5385-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="b5385-138">ASP.NET Core では、アプリケーションへのエントリ ポイントは `Startup` であり、*Global.asax* に依存する必要はなくなりました。</span><span class="sxs-lookup"><span data-stu-id="b5385-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="b5385-139">.NET Framework で OWIN を使うときは、パイプラインとして次のようなものを使います。</span><span class="sxs-lookup"><span data-stu-id="b5385-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="b5385-140">これにより既定のルートが構成され、既定では Json 経由の XmlSerialization です。</span><span class="sxs-lookup"><span data-stu-id="b5385-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="b5385-141">必要に応じて、このパイプラインに他のミドルウェアを追加します (サービスの読み込み、構成設定、静的ファイルなど)。</span><span class="sxs-lookup"><span data-stu-id="b5385-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="b5385-142">ASP.NET Core は同様のアプローチを使いますが、エントリを処理するために OWIN には依存しません。</span><span class="sxs-lookup"><span data-stu-id="b5385-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="b5385-143">代わりに、(コンソール アプリケーションと同じように) *Program.cs* の `Main` メソッドを通して行われ、そこから `Startup` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="b5385-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="b5385-144">`Startup` は、`Configure` メソッドを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5385-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="b5385-145">`Configure` では、必要なミドルウェアをパイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="b5385-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="b5385-146">(既定の Web サイト テンプレートからの) 次の例では、拡張メソッドにより、以下をサポートするパイプラインが構成されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="b5385-147">エラー ページ</span><span class="sxs-lookup"><span data-stu-id="b5385-147">Error pages</span></span>
- <span data-ttu-id="b5385-148">HTTP Strict Transport Security</span><span class="sxs-lookup"><span data-stu-id="b5385-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="b5385-149">HTTP への HTTP リダイレクト</span><span class="sxs-lookup"><span data-stu-id="b5385-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="b5385-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="b5385-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="b5385-151">ホストとアプリケーションは切り離されており、将来別のプラットフォームに柔軟に移動できます。</span><span class="sxs-lookup"><span data-stu-id="b5385-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="b5385-152">ASP.NET Core のスタートアップとミドルウェアについて詳しくは、「[ASP.NET Core でのアプリケーションのスタートアップ](xref:fundamentals/startup)」をご覧ください</span><span class="sxs-lookup"><span data-stu-id="b5385-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="b5385-153">構成を保存する</span><span class="sxs-lookup"><span data-stu-id="b5385-153">Store configurations</span></span>

<span data-ttu-id="b5385-154">ASP.NET では保存の設定がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b5385-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="b5385-155">これらの設定は、たとえば、アプリケーションが展開された環境のサポートに使われます。</span><span class="sxs-lookup"><span data-stu-id="b5385-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="b5385-156">一般的な方法は、すべてのカスタム キー/値ペアを、*Web.config* ファイルの `<appSettings>` セクションに保存するというものでした。</span><span class="sxs-lookup"><span data-stu-id="b5385-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="b5385-157">アプリケーションでは、`System.Configuration` 名前空間内の `ConfigurationManager.AppSettings` コレクションを使ってこれらの設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b5385-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="b5385-158">ASP.NET Core では、アプリケーションの構成データを任意のファイルに保存し、ミドルウェアのブートストラップの一部として読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="b5385-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="b5385-159">プロジェクト テンプレートで使われる既定のファイルは、*appsettings.json* です。</span><span class="sxs-lookup"><span data-stu-id="b5385-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="b5385-160">アプリケーション内部の `IConfiguration` のインスタンスにこのファイルを読み込むには、*Startup.cs* が使われます。</span><span class="sxs-lookup"><span data-stu-id="b5385-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="b5385-161">アプリでは、`Configuration` から読み取って設定を取得します。</span><span class="sxs-lookup"><span data-stu-id="b5385-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="b5385-162">このアプローチにはプロセスをより堅牢にする拡張機能があります。たとえば、[依存性の注入](xref:fundamentals/dependency-injection) (DI) を使ってサービスとこれらの値を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="b5385-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="b5385-163">DI アプローチは、厳密に型指定された構成オブジェクトのセットを提供します。</span><span class="sxs-lookup"><span data-stu-id="b5385-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="b5385-164">ASP.NET Core の構成について詳しくは、「[ASP.NET Core の構成](xref:fundamentals/configuration/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b5385-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="b5385-165">ネイティブな依存性の注入</span><span class="sxs-lookup"><span data-stu-id="b5385-165">Native dependency injection</span></span>

<span data-ttu-id="b5385-166">大規模で拡張性の高いアプリケーションを構築するときの重要な目標は、コンポーネントとサービスの疎な結合です。</span><span class="sxs-lookup"><span data-stu-id="b5385-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="b5385-167">[依存性の注入](xref:fundamentals/dependency-injection)はこれを実現するための一般的な手法であり、ASP.NET Core のネイティブなコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="b5385-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="b5385-168">ASP.NET アプリでは、開発者はサードパーティのライブラリに依存して依存性の注入を実装します。</span><span class="sxs-lookup"><span data-stu-id="b5385-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="b5385-169">[Unity](https://github.com/unitycontainer/unity) はそのようなライブラリの 1 つであり、Microsoft Patterns & Practices によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="b5385-170">Unity で依存性の注入を設定する例は、`UnityContainer` をラップする `IDependencyResolver` の実装です。</span><span class="sxs-lookup"><span data-stu-id="b5385-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="b5385-171">`UnityContainer` のインスタンスを作成し、サービスを登録して、`HttpConfiguration` の依存関係リゾルバーをコンテナー用の `UnityResolver` の新しいインスタンスに設定します。</span><span class="sxs-lookup"><span data-stu-id="b5385-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="b5385-172">必要な場所に `IProductRepository` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="b5385-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="b5385-173">依存性の注入は ASP.NET Core の一部であるため、*Startup.cs* の `ConfigureServices` メソッドに独自のサービスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b5385-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="b5385-174">Unity でそうであったように、リポジトリは任意の場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="b5385-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="b5385-175">依存関係の挿入について詳しくは、「[依存関係の挿入](xref:fundamentals/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b5385-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="b5385-176">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="b5385-176">Serve static files</span></span>

<span data-ttu-id="b5385-177">Web 開発の重要な部分は、静的なクライアント側アセットを提供する機能です。</span><span class="sxs-lookup"><span data-stu-id="b5385-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="b5385-178">静的なファイルの最も一般的な例は、HTML、CSS、Javascript、およびイメージです。</span><span class="sxs-lookup"><span data-stu-id="b5385-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="b5385-179">これらのファイルは、アプリ (または CDN) の公開された場所に保存され、要求によって読み込めるように参照される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5385-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="b5385-180">このプロセスは、ASP.NET Core で変更されました。</span><span class="sxs-lookup"><span data-stu-id="b5385-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="b5385-181">ASP.NET では、静的ファイルはさまざまなディレクトリに保存され、ビューで参照されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="b5385-182">ASP.NET Core では、構成が変更されていない限り、静的ファイルは "Web ルート" ( *&lt;コンテンツ ルート&gt;/wwwroot*) に保存されます。</span><span class="sxs-lookup"><span data-stu-id="b5385-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="b5385-183">ファイルは、`Startup.Configure` から `UseStaticFiles` 拡張メソッドを呼び出すことによって、要求パイプラインに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="b5385-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="b5385-184">.NET Framework を対象にする場合は、NuGet パッケージ `Microsoft.AspNetCore.StaticFiles` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b5385-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="b5385-185">たとえば、*wwwroot/images* フォルダー内のイメージ アセットには、ブラウザーから `http://<app>/images/<imageFileName>` などの場所でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b5385-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="b5385-186">ASP.NET Core での静的ファイルの提供について詳しくは、[静的ファイル](xref:fundamentals/static-files)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b5385-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="b5385-187">複数値の cookie</span><span class="sxs-lookup"><span data-stu-id="b5385-187">Multi-value cookies</span></span>

<span data-ttu-id="b5385-188">[複数値の cookie](xref:System.Web.HttpCookie.Values) は ASP.NET Core ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b5385-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="b5385-189">値ごとに cookie を 1 つ作成します。</span><span class="sxs-lookup"><span data-stu-id="b5385-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="b5385-190">部分的なアプリの移行</span><span class="sxs-lookup"><span data-stu-id="b5385-190">Partial app migration</span></span>

<span data-ttu-id="b5385-191">部分的にアプリを移行する方法の 1 つは、IIS サブアプリケーションを作成し、アプリの URL 構造を維持しながら ASP.NET 4.x から ASP.NET Core に特定のルートのみを移動することです。</span><span class="sxs-lookup"><span data-stu-id="b5385-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="b5385-192">たとえば、*applicationHost.config* ファイルからのアプリの URL 構造について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b5385-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="b5385-193">ディレクトリの構造:</span><span class="sxs-lookup"><span data-stu-id="b5385-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="b5385-194">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b5385-194">Additional resources</span></span>

- [<span data-ttu-id="b5385-195">.NET Core にライブラリを移植する</span><span class="sxs-lookup"><span data-stu-id="b5385-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
