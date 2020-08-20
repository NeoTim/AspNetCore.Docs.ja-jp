---
title: ファイル ウォッチャーを使用した ASP.NET Core アプリの開発
author: rick-anderson
description: このチュートリアルでは、.NET Core CLI のファイル ウォッチャー (dotnet watch) ツールをインストールし、ASP.NET Core アプリで使用する方法について説明します。
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: cc152c2ca553b00619ddbf829f6044867c53bb98
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635139"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="1feac-103">ファイル ウォッチャーを使用した ASP.NET Core アプリの開発</span><span class="sxs-lookup"><span data-stu-id="1feac-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="1feac-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="1feac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="1feac-105">`dotnet watch` は、ソース ファイルの変更時に [.NET Core CLI](/dotnet/core/tools) コマンドを実行するツールです。</span><span class="sxs-lookup"><span data-stu-id="1feac-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="1feac-106">たとえば、あるファイルを変更すると、コンパイル、テストの実行、展開が開始されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="1feac-107">このチュートリアルでは、エンドポイントが 2 つの既存の Web API を利用します。合計を返すエンドポイントと積を返すエンドポイントです。</span><span class="sxs-lookup"><span data-stu-id="1feac-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="1feac-108">積のメソッドにはバグがあり、このチュートリアルで修正します。</span><span class="sxs-lookup"><span data-stu-id="1feac-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="1feac-109">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)をダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="1feac-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="1feac-110">これには次の 2 つのプロジェクトが含まれています。*WebApp* (ASP.NET Core Web API) および *WebAppTests* (Web API の単体テスト)。</span><span class="sxs-lookup"><span data-stu-id="1feac-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="1feac-111">コマンド シェルで、*WebApp* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="1feac-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="1feac-112">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="1feac-113">`dotnet run --project <PROJECT>` を使用して、実行するプロジェクトを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="1feac-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="1feac-114">たとえば、サンプル アプリのルートから `dotnet run --project WebApp` を実行すると、*WebApp* プロジェクトも実行されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="1feac-115">コンソール出力に、次のようなメッセージが表示されます。アプリが実行中であり、要求を待っていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="1feac-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="1feac-116">Web ブラウザーで、`http://localhost:<port number>/api/math/sum?a=4&b=5` に移動します。</span><span class="sxs-lookup"><span data-stu-id="1feac-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="1feac-117">結果として `9` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-117">You should see the result of `9`.</span></span>

<span data-ttu-id="1feac-118">製品 API に移動します (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="1feac-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="1feac-119">予想していた `20` ではなく、`9` が返されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="1feac-120">この問題は、チュートリアルで後ほど修正します。</span><span class="sxs-lookup"><span data-stu-id="1feac-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="1feac-121">`dotnet watch` をプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="1feac-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="1feac-122">`dotnet watch` ファイル ウォッチャー ツールは、.NET Core SDK のバージョン 2.1.300 に付属しています。</span><span class="sxs-lookup"><span data-stu-id="1feac-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="1feac-123">これより前のバージョンの .NET Core SDK を使用する場合は、次の手順が必要です。</span><span class="sxs-lookup"><span data-stu-id="1feac-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="1feac-124">`Microsoft.DotNet.Watcher.Tools` パッケージ参照を *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1feac-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="1feac-125">次のコマンドを実行して `Microsoft.DotNet.Watcher.Tools` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1feac-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="1feac-126">`dotnet watch` を使用した .NET Core CLI コマンドの実行</span><span class="sxs-lookup"><span data-stu-id="1feac-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="1feac-127">[.NET Core CLI コマンド](/dotnet/core/tools#cli-commands) はいずれも、`dotnet watch` との組み合わせで実行することができます。</span><span class="sxs-lookup"><span data-stu-id="1feac-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="1feac-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1feac-128">For example:</span></span>

| <span data-ttu-id="1feac-129">コマンド</span><span class="sxs-lookup"><span data-stu-id="1feac-129">Command</span></span> | <span data-ttu-id="1feac-130">コマンドと watch</span><span class="sxs-lookup"><span data-stu-id="1feac-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="1feac-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="1feac-131">dotnet run</span></span> | <span data-ttu-id="1feac-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="1feac-132">dotnet watch run</span></span> |
| <span data-ttu-id="1feac-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="1feac-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="1feac-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="1feac-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="1feac-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="1feac-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="1feac-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="1feac-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="1feac-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="1feac-137">dotnet test</span></span> | <span data-ttu-id="1feac-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="1feac-138">dotnet watch test</span></span> |

<span data-ttu-id="1feac-139">*WebApp* フォルダーの `dotnet watch run` を実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="1feac-140">コンソール出力に、`watch` が起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="1feac-141">`dotnet watch --project <PROJECT>` を使用して、ウォッチするプロジェクトを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="1feac-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="1feac-142">たとえば、サンプル アプリのルートから `dotnet watch --project WebApp run` を実行すると、*WebApp* プロジェクトも実行されてウォッチされます。</span><span class="sxs-lookup"><span data-stu-id="1feac-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="1feac-143">`dotnet watch` で変更を行う</span><span class="sxs-lookup"><span data-stu-id="1feac-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="1feac-144">`dotnet watch` が実行されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1feac-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="1feac-145">*MathController.cs* の `Product` メソッドのバグを修正して、合計ではなく積を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="1feac-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="1feac-146">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="1feac-146">Save the file.</span></span> <span data-ttu-id="1feac-147">コンソール出力により、`dotnet watch` がファイル変更を検出し、アプリを再起動したことが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="1feac-148">`http://localhost:<port number>/api/math/product?a=4&b=5` が正しい結果を返すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="1feac-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="1feac-149">`dotnet watch` を使用してテストを実行する</span><span class="sxs-lookup"><span data-stu-id="1feac-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="1feac-150">*MathController.cs* の `Product` メソッドを元に戻して合計を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="1feac-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="1feac-151">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="1feac-151">Save the file.</span></span>
1. <span data-ttu-id="1feac-152">コマンド シェルで、*WebAppTests* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="1feac-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="1feac-153">[dotnet restore](/dotnet/core/tools/dotnet-restore) を実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="1feac-154">`dotnet watch test` を実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="1feac-155">テストに失敗し、ウォッチャーがファイル変更を待っていることが出力に示されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="1feac-156">積を返すように `Product` メソッドのコードを修正します。</span><span class="sxs-lookup"><span data-stu-id="1feac-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="1feac-157">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="1feac-157">Save the file.</span></span>

<span data-ttu-id="1feac-158">`dotnet watch` はファイル変更を検出し、テストを再実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="1feac-159">コンソール出力にテストの合格が示されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="1feac-160">監視するファイル リストのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="1feac-160">Customize files list to watch</span></span>

<span data-ttu-id="1feac-161">既定では、`dotnet-watch` は次の glob パターンに一致するすべてのファイルを追跡します。</span><span class="sxs-lookup"><span data-stu-id="1feac-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="1feac-162">ウォッチ リストに他の項目を追加するには、 *.csproj* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="1feac-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="1feac-163">項目は個別に指定することも、glob パターンを使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="1feac-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="1feac-164">ウォッチするファイルのオプトアウト</span><span class="sxs-lookup"><span data-stu-id="1feac-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="1feac-165">既定の設定を無視するように `dotnet-watch` を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1feac-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="1feac-166">特定のファイルを無視するには、 *.csproj* ファイルで項目の定義に `Watch="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="1feac-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="1feac-167">カスタム ウォッチ プロジェクト</span><span class="sxs-lookup"><span data-stu-id="1feac-167">Custom watch projects</span></span>

<span data-ttu-id="1feac-168">`dotnet-watch` は C# プロジェクトだけに限定されていません。</span><span class="sxs-lookup"><span data-stu-id="1feac-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="1feac-169">カスタム ウォッチ プロジェクトは、さまざまなシナリオを処理するために作成できます。</span><span class="sxs-lookup"><span data-stu-id="1feac-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="1feac-170">次のプロジェクト レイアウトを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="1feac-170">Consider the following project layout:</span></span>

* <span data-ttu-id="1feac-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="1feac-171">**test/**</span></span>
  * <span data-ttu-id="1feac-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="1feac-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="1feac-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="1feac-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="1feac-174">両方のプロジェクトを監視するのが目的である場合、両方のプロジェクトを監視するように構成されたカスタム プロジェクト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="1feac-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="1feac-175">両方のプロジェクトでファイルの監視を開始するには、*test* フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="1feac-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="1feac-176">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1feac-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="1feac-177">VSTest は、いずれかのテスト プロジェクトでファイルが変更されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="1feac-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="1feac-178">GitHub での `dotnet-watch`</span><span class="sxs-lookup"><span data-stu-id="1feac-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="1feac-179">`dotnet-watch` は GitHub の [aspnet/AspNetCore リポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="1feac-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
