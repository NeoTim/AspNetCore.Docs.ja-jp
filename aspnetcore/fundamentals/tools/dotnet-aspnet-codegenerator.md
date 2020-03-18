---
title: dotnet aspnet-codegenerator コマンド
author: rick-anderson
description: dotnet aspnet-codegenerator コマンドでは、ASP.NET Core プロジェクトがスキャフォールディングされます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649856"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="76300-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="76300-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="76300-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76300-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76300-105">`dotnet aspnet-codegenerator` - ASP.NET Core のスキャフォールディング エンジンを実行します。</span><span class="sxs-lookup"><span data-stu-id="76300-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="76300-106">`dotnet aspnet-codegenerator` が必要になるのは、コマンド ラインからスキャフォールディングする場合のみです。Visual Studio でスキャフォールディングする場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="76300-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="76300-107">この記事は [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 以降に適用されます。</span><span class="sxs-lookup"><span data-stu-id="76300-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="76300-108">aspnet-codegenerator のインストール</span><span class="sxs-lookup"><span data-stu-id="76300-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="76300-109">`dotnet-aspnet-codegenerator` は[グローバル ツール](/dotnet/core/tools/global-tools)であり、インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="76300-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="76300-110">次のコマンドを使うと、`dotnet-aspnet-codegenerator` ツールの最新の安定バージョンをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="76300-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="76300-111">次のコマンドを使うと、インストール済みの .NET Core SDK から利用できる更新の安定バージョンに `dotnet-aspnet-codegenerator` を更新できます。</span><span class="sxs-lookup"><span data-stu-id="76300-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="76300-112">構文</span><span class="sxs-lookup"><span data-stu-id="76300-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="76300-113">説明</span><span class="sxs-lookup"><span data-stu-id="76300-113">Description</span></span>

<span data-ttu-id="76300-114">`dotnet aspnet-codegenerator` グローバル コマンドを使うと、ASP.NET Core のコード ジェネレーターとスキャフォールディング エンジンが実行されます。</span><span class="sxs-lookup"><span data-stu-id="76300-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="76300-115">引数</span><span class="sxs-lookup"><span data-stu-id="76300-115">Arguments</span></span>

`generator`

<span data-ttu-id="76300-116">実行するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="76300-116">The code generator to run.</span></span> <span data-ttu-id="76300-117">次のジェネレーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="76300-117">The following generators are available:</span></span>

| <span data-ttu-id="76300-118">Generator</span><span class="sxs-lookup"><span data-stu-id="76300-118">Generator</span></span> | <span data-ttu-id="76300-119">操作</span><span class="sxs-lookup"><span data-stu-id="76300-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="76300-120">area</span><span class="sxs-lookup"><span data-stu-id="76300-120">area</span></span>      | [<span data-ttu-id="76300-121">区分のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="76300-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="76300-122">コントローラー</span><span class="sxs-lookup"><span data-stu-id="76300-122">controller</span></span>| [<span data-ttu-id="76300-123">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="76300-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="76300-124">ID</span><span class="sxs-lookup"><span data-stu-id="76300-124">identity</span></span>  | [<span data-ttu-id="76300-125">Identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="76300-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="76300-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="76300-126">razorpage</span></span> | [<span data-ttu-id="76300-127">Razor Pages のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="76300-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="76300-128">ビュー</span><span class="sxs-lookup"><span data-stu-id="76300-128">view</span></span>      | [<span data-ttu-id="76300-129">ビューのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="76300-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="76300-130">オプション</span><span class="sxs-lookup"><span data-stu-id="76300-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="76300-131">NuGet パッケージのディレクトリを指定します。</span><span class="sxs-lookup"><span data-stu-id="76300-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="76300-132">ビルド構成を定義します。</span><span class="sxs-lookup"><span data-stu-id="76300-132">Defines the build configuration.</span></span> <span data-ttu-id="76300-133">既定値は `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="76300-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="76300-134">使用するターゲット [フレームワーク](/dotnet/standard/frameworks)です。</span><span class="sxs-lookup"><span data-stu-id="76300-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="76300-135">たとえば、`net46` のようにします。</span><span class="sxs-lookup"><span data-stu-id="76300-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="76300-136">ビルドのベース パスです。</span><span class="sxs-lookup"><span data-stu-id="76300-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="76300-137">コマンドの短いヘルプを印刷します。</span><span class="sxs-lookup"><span data-stu-id="76300-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="76300-138">実行前にプロジェクトをビルドしません。</span><span class="sxs-lookup"><span data-stu-id="76300-138">Doesn't build the project before running.</span></span> <span data-ttu-id="76300-139">また、`--no-restore` フラグが暗黙的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="76300-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="76300-140">実行するプロジェクト ファイルのパスを指定します (フォルダー名または完全なパス)。</span><span class="sxs-lookup"><span data-stu-id="76300-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="76300-141">指定しない場合は、既定で現在のディレクトリに設定されます。</span><span class="sxs-lookup"><span data-stu-id="76300-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="76300-142">ジェネレーターのオプション</span><span class="sxs-lookup"><span data-stu-id="76300-142">Generator options</span></span>

<span data-ttu-id="76300-143">以下のセクションでは、次のサポートされているジェネレーターで使用できるオプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="76300-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="76300-144">区分</span><span class="sxs-lookup"><span data-stu-id="76300-144">Area</span></span>
* <span data-ttu-id="76300-145">コントローラー</span><span class="sxs-lookup"><span data-stu-id="76300-145">Controller</span></span>
* <span data-ttu-id="76300-146">Identity</span><span class="sxs-lookup"><span data-stu-id="76300-146">Identity</span></span>  
* <span data-ttu-id="76300-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="76300-147">Razorpage</span></span>
* <span data-ttu-id="76300-148">View</span><span class="sxs-lookup"><span data-stu-id="76300-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="76300-149">Area のオプション</span><span class="sxs-lookup"><span data-stu-id="76300-149">Area options</span></span>

<span data-ttu-id="76300-150">このツールは、コントローラーとビューを含む ASP.NET Core Web プロジェクト用のものです。</span><span class="sxs-lookup"><span data-stu-id="76300-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="76300-151">Razor Pages アプリ用ではありません。</span><span class="sxs-lookup"><span data-stu-id="76300-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="76300-152">使用方法: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="76300-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="76300-153">上のコマンドを実行すると、次のフォルダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76300-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="76300-154">*領域*</span><span class="sxs-lookup"><span data-stu-id="76300-154">*Areas*</span></span>
  * <span data-ttu-id="76300-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="76300-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="76300-156">*コントローラー*</span><span class="sxs-lookup"><span data-stu-id="76300-156">*Controllers*</span></span>
    * <span data-ttu-id="76300-157">*データ*</span><span class="sxs-lookup"><span data-stu-id="76300-157">*Data*</span></span>
    * <span data-ttu-id="76300-158">*Models*</span><span class="sxs-lookup"><span data-stu-id="76300-158">*Models*</span></span>
    * <span data-ttu-id="76300-159">*ビュー*</span><span class="sxs-lookup"><span data-stu-id="76300-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="76300-160">Controller のオプション</span><span class="sxs-lookup"><span data-stu-id="76300-160">Controller options</span></span>

<span data-ttu-id="76300-161">`aspnet-codegenerator` `controller` および `razorpage` 用のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="76300-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="76300-162">`aspnet-codegenerator controller` に固有のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="76300-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="76300-163">オプション</span><span class="sxs-lookup"><span data-stu-id="76300-163">Option</span></span>               | <span data-ttu-id="76300-164">説明</span><span class="sxs-lookup"><span data-stu-id="76300-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="76300-165">--controllerName または -name</span><span class="sxs-lookup"><span data-stu-id="76300-165">--controllerName or -name</span></span> | <span data-ttu-id="76300-166">コントローラーの名前です。</span><span class="sxs-lookup"><span data-stu-id="76300-166">Name of the controller.</span></span> |
| <span data-ttu-id="76300-167">--useAsyncActions または -async</span><span class="sxs-lookup"><span data-stu-id="76300-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="76300-168">非同期コントローラー アクションを生成します。</span><span class="sxs-lookup"><span data-stu-id="76300-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="76300-169">--noViews または -nv</span><span class="sxs-lookup"><span data-stu-id="76300-169">--noViews or -nv</span></span> | <span data-ttu-id="76300-170">ビューを生成**しません**。</span><span class="sxs-lookup"><span data-stu-id="76300-170">Generate **no** views.</span></span> |
| <span data-ttu-id="76300-171">--restWithNoViews または -api</span><span class="sxs-lookup"><span data-stu-id="76300-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="76300-172">REST スタイルの API でコントローラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="76300-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="76300-173">`noViews` を前提とし、ビュー関連のオプションはすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="76300-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="76300-174">--readWriteActions または -actions</span><span class="sxs-lookup"><span data-stu-id="76300-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="76300-175">モデルなしで読み取り/書き込みアクションを備えたコントローラーを生成します。</span><span class="sxs-lookup"><span data-stu-id="76300-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="76300-176">`aspnet-codegenerator controller` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。</span><span class="sxs-lookup"><span data-stu-id="76300-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="76300-177">`dotnet aspnet-codegenerator controller` の例については、[映画モデルのスキャフォールディング](/aspnet/core/tutorials/razor-pages/model)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="76300-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="76300-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="76300-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="76300-179">新しいページの名前と使用するテンプレートを指定すれば、Razor Pages を個別にスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="76300-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="76300-180">サポートされているテンプレートは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76300-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="76300-181">たとえば、次のコマンドでは、編集テンプレートを使って *MyEdit.cshtml* と *MyEdit.cshtml.cs* が生成されます。</span><span class="sxs-lookup"><span data-stu-id="76300-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="76300-182">通常、テンプレートと生成されたファイル名は指定されず、次のテンプレートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="76300-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="76300-183">`aspnet-codegenerator` `razorpage` および `controller` 用のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="76300-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="76300-184">`aspnet-codegenerator razorpage` に固有のオプションの一覧を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="76300-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="76300-185">オプション</span><span class="sxs-lookup"><span data-stu-id="76300-185">Option</span></span>               | <span data-ttu-id="76300-186">説明</span><span class="sxs-lookup"><span data-stu-id="76300-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="76300-187">--namespaceName または -namespace</span><span class="sxs-lookup"><span data-stu-id="76300-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="76300-188">生成された PageModel に対して使用する名前空間の名前です</span><span class="sxs-lookup"><span data-stu-id="76300-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="76300-189">--partialView または -partial</span><span class="sxs-lookup"><span data-stu-id="76300-189">--partialView or -partial</span></span> | <span data-ttu-id="76300-190">部分ビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="76300-190">Generate a partial view.</span></span> <span data-ttu-id="76300-191">これを指定した場合、レイアウトのオプション -l と -udl は無視されます。</span><span class="sxs-lookup"><span data-stu-id="76300-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="76300-192">--noPageModel または -npm</span><span class="sxs-lookup"><span data-stu-id="76300-192">--noPageModel or -npm</span></span> | <span data-ttu-id="76300-193">空のテンプレートの PageModel クラスを生成しない動作に切り替えます</span><span class="sxs-lookup"><span data-stu-id="76300-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="76300-194">`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。</span><span class="sxs-lookup"><span data-stu-id="76300-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="76300-195">`dotnet aspnet-codegenerator razorpage` の例については、[映画モデルのスキャフォールディング](/aspnet/core/tutorials/razor-pages/model)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="76300-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="76300-196">Identity</span><span class="sxs-lookup"><span data-stu-id="76300-196">Identity</span></span>

<span data-ttu-id="76300-197">[Identity のスキャフォールディング](/aspnet/core/security/authentication/scaffold-identity)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="76300-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
