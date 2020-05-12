---
title: 'チュートリアル: ASP.NET Core の Razor Pages の概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、移行を使用したモデルの更新の方法について学習します。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774874"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="84435-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="84435-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="84435-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84435-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="84435-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="84435-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="84435-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84435-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="84435-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="84435-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="84435-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="84435-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="84435-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-110">Run the app.</span></span>
> * <span data-ttu-id="84435-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="84435-111">Examine the project files.</span></span>

<span data-ttu-id="84435-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84435-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="84435-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84435-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84435-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84435-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="84435-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84435-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84435-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84435-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="84435-118">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84435-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84435-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84435-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84435-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="84435-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="84435-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="84435-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="84435-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="84435-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="84435-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="84435-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="84435-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="84435-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="84435-126">ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="84435-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="84435-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84435-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="84435-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="84435-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="84435-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="84435-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="84435-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="84435-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="84435-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="84435-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="84435-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="84435-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="84435-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-137">Select **Yes**.</span></span>

  <span data-ttu-id="84435-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="84435-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84435-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84435-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="84435-140">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-140">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="84435-142">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="84435-144">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="84435-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="84435-146">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="84435-148">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="84435-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="84435-149">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="84435-149">Examine the project files</span></span>

<span data-ttu-id="84435-150">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="84435-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="84435-151">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="84435-151">Pages folder</span></span>

<span data-ttu-id="84435-152">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="84435-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="84435-153">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="84435-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="84435-154">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="84435-155">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="84435-156">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="84435-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="84435-157">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="84435-158">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="84435-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="84435-159">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="84435-160">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="84435-160">wwwroot folder</span></span>

<span data-ttu-id="84435-161">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="84435-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="84435-162">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="84435-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="84435-163">appSettings.json</span></span>

<span data-ttu-id="84435-164">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="84435-165">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="84435-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="84435-166">Program.cs</span></span>

<span data-ttu-id="84435-167">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-167">Contains the entry point for the program.</span></span> <span data-ttu-id="84435-168">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="84435-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="84435-169">Startup.cs</span></span>

<span data-ttu-id="84435-170">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="84435-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="84435-171">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="84435-172">次の手順</span><span class="sxs-lookup"><span data-stu-id="84435-172">Next steps</span></span>

<span data-ttu-id="84435-173">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="84435-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="84435-174">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="84435-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84435-175">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="84435-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="84435-176">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="84435-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="84435-177">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84435-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="84435-178">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="84435-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="84435-179">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="84435-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="84435-180">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-180">Run the app.</span></span>
> * <span data-ttu-id="84435-181">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="84435-181">Examine the project files.</span></span>

<span data-ttu-id="84435-182">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84435-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="84435-184">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84435-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84435-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84435-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="84435-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84435-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84435-187">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84435-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="84435-188">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="84435-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84435-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84435-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84435-190">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="84435-191">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="84435-193">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="84435-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="84435-194">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="84435-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="84435-196">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="84435-198">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-198">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="84435-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84435-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="84435-201">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="84435-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="84435-202">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="84435-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="84435-203">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="84435-204">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="84435-205">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="84435-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="84435-206">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="84435-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="84435-207">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-207">Select **Yes**.</span></span>

  <span data-ttu-id="84435-208">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="84435-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84435-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84435-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="84435-210">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-210">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="84435-212">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="84435-214">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="84435-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="84435-216">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84435-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="84435-218">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="84435-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84435-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84435-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84435-220">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="84435-221">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="84435-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="84435-222">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84435-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84435-223">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84435-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="84435-224">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="84435-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="84435-225">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="84435-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="84435-226">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84435-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="84435-227">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="84435-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="84435-229">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84435-229">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="84435-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84435-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="84435-232">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="84435-233">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="84435-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="84435-234">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84435-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84435-235">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84435-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="84435-236">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="84435-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="84435-237">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84435-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="84435-238">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="84435-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="84435-240">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84435-240">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84435-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84435-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="84435-243">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="84435-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="84435-244">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="84435-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="84435-245">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84435-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="84435-246">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="84435-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="84435-248">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84435-248">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="84435-250">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="84435-250">Examine the project files</span></span>

<span data-ttu-id="84435-251">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="84435-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="84435-252">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="84435-252">Pages folder</span></span>

<span data-ttu-id="84435-253">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="84435-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="84435-254">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="84435-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="84435-255">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="84435-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="84435-256">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="84435-257">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="84435-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="84435-258">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="84435-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="84435-259">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="84435-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="84435-260">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="84435-261">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="84435-261">wwwroot folder</span></span>

<span data-ttu-id="84435-262">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="84435-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="84435-263">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="84435-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="84435-264">appSettings.json</span></span>

<span data-ttu-id="84435-265">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="84435-266">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="84435-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="84435-267">Program.cs</span></span>

<span data-ttu-id="84435-268">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-268">Contains the entry point for the program.</span></span> <span data-ttu-id="84435-269">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="84435-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="84435-270">Startup.cs</span></span>

<span data-ttu-id="84435-271">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="84435-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="84435-272">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84435-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84435-273">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="84435-273">Additional resources</span></span>

* [<span data-ttu-id="84435-274">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="84435-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="84435-275">次の手順</span><span class="sxs-lookup"><span data-stu-id="84435-275">Next steps</span></span>

<span data-ttu-id="84435-276">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="84435-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="84435-277">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="84435-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
