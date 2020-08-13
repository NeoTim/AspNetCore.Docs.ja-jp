---
title: ASP.NET Core と MongoDB で Web API を作成する
author: prkhandelwal
description: このチュートリアルは、MongoDB NoSQL データベースを使用して ASP.NET Core Web API を作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: fac4adb6ffa487c7ca9650a8ebd063dd344d84a7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020250"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="b4011-103">ASP.NET Core と MongoDB で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="b4011-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="b4011-104">作成者: [Pratik Khandelwal](https://twitter.com/K2Prk) および [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b4011-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b4011-105">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="b4011-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b4011-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b4011-107">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-107">Configure MongoDB</span></span>
> * <span data-ttu-id="b4011-108">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="b4011-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="b4011-109">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="b4011-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="b4011-110">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="b4011-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="b4011-111">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="b4011-111">Customize JSON serialization</span></span>

<span data-ttu-id="b4011-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b4011-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4011-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b4011-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4011-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4011-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="b4011-115">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="b4011-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b4011-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="b4011-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b4011-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b4011-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b4011-119">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="b4011-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b4011-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b4011-121">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="b4011-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="b4011-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b4011-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4011-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b4011-124">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="b4011-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b4011-125">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="b4011-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="b4011-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="b4011-127">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-127">Configure MongoDB</span></span>

<span data-ttu-id="b4011-128">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b4011-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="b4011-129">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="b4011-130">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="b4011-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="b4011-131">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="b4011-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="b4011-132">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b4011-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="b4011-133">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="b4011-134">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="b4011-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="b4011-135">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="b4011-136">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b4011-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="b4011-137">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-137">Open a command shell.</span></span> <span data-ttu-id="b4011-138">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="b4011-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="b4011-139">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b4011-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="b4011-140">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-140">Open another command shell instance.</span></span> <span data-ttu-id="b4011-141">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="b4011-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="b4011-142">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="b4011-143">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="b4011-144">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="b4011-145">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="b4011-146">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="b4011-147">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="b4011-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="b4011-148">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="b4011-149">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="b4011-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="b4011-150">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="b4011-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="b4011-151">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="b4011-152">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="b4011-153">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="b4011-153">The database is ready.</span></span> <span data-ttu-id="b4011-154">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="b4011-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="b4011-155">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b4011-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4011-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4011-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b4011-157">**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b4011-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b4011-158">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-159">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-160">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="b4011-161">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-162">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4011-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b4011-163">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="b4011-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="b4011-164">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4011-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b4011-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b4011-166">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="b4011-167">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="b4011-168">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="b4011-169">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-169">Select **Yes**.</span></span>
1. <span data-ttu-id="b4011-170">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4011-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b4011-171">**[統合ターミナル]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="b4011-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="b4011-172">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4011-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b4011-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4011-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b4011-174">バージョン 8.6 より前の Visual Studio for Mac の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[.NET Core]** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="b4011-175">バージョン 8.6 以降の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[Web and Console]\(Web とコンソール)** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="b4011-176">**[ASP.NET Core]** 、 **[API]** C# プロジェクト テンプレートの順に選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-177">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 3.1]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-178">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-179">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="b4011-180">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="b4011-181">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="b4011-182">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-182">Add an entity model</span></span>

1. <span data-ttu-id="b4011-183">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="b4011-184">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="b4011-185">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="b4011-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="b4011-186">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="b4011-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="b4011-187">ドキュメントの主キーとしてこのプロパティを指定するために、[`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="b4011-188">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく `string` 型としてパラメーターを渡すことができるようにするために、[`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="b4011-189">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="b4011-190">`BookName` プロパティには、[`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="b4011-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="b4011-191">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="b4011-192">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-192">Add a configuration model</span></span>

1. <span data-ttu-id="b4011-193">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="b4011-194">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="b4011-195">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="b4011-196">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="b4011-197">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="b4011-198">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b4011-198">In the preceding code:</span></span>

   * <span data-ttu-id="b4011-199">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="b4011-200">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="b4011-201">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b4011-202">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="b4011-203">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="b4011-204">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="b4011-205">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="b4011-206">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="b4011-207">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="b4011-208">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b4011-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="b4011-209">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="b4011-210">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="b4011-211">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="b4011-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="b4011-212">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="b4011-213">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="b4011-214">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="b4011-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm):データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b4011-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="b4011-216">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="b4011-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm):操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="b4011-218">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使用し、特定のコレクション内のデータへのアクセスを取得します。</span><span class="sxs-lookup"><span data-stu-id="b4011-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="b4011-219">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="b4011-220">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b4011-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="b4011-221">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="b4011-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="b4011-222">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="b4011-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="b4011-223">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="b4011-224">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b4011-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="b4011-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm):指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="b4011-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="b4011-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm):指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="b4011-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm):指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="b4011-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="b4011-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm):指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b4011-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b4011-229">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="b4011-229">Add a controller</span></span>

<span data-ttu-id="b4011-230">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="b4011-231">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="b4011-231">The preceding web API controller:</span></span>

* <span data-ttu-id="b4011-232">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="b4011-233">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b4011-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="b4011-234">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="b4011-235">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="b4011-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="b4011-236">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="b4011-237">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="b4011-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="b4011-238">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="b4011-238">Test the web API</span></span>

1. <span data-ttu-id="b4011-239">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-239">Build and run the app.</span></span>

1. <span data-ttu-id="b4011-240">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4011-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="b4011-241">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="b4011-242">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4011-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="b4011-243">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="b4011-244">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-244">Configure JSON serialization options</span></span>

<span data-ttu-id="b4011-245">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="b4011-246">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="b4011-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="b4011-247">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="b4011-248">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="b4011-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="b4011-249">JSON.NET は ASP.NET 共有フレームワークから削除されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="b4011-250">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="b4011-251">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddControllers` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="b4011-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="b4011-252">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="b4011-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="b4011-253">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="b4011-254">*Models/Book.cs* では、`BookName` プロパティに次の [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4011-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="b4011-255">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="b4011-256">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="b4011-257">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="b4011-258">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b4011-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4011-259">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="b4011-260">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b4011-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b4011-261">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-261">Configure MongoDB</span></span>
> * <span data-ttu-id="b4011-262">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="b4011-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="b4011-263">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="b4011-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="b4011-264">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="b4011-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="b4011-265">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="b4011-265">Customize JSON serialization</span></span>

<span data-ttu-id="b4011-266">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b4011-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4011-267">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b4011-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4011-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4011-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="b4011-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b4011-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b4011-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="b4011-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b4011-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b4011-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b4011-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b4011-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b4011-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b4011-275">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="b4011-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="b4011-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b4011-277">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4011-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b4011-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b4011-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b4011-279">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="b4011-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="b4011-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b4011-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="b4011-281">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-281">Configure MongoDB</span></span>

<span data-ttu-id="b4011-282">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b4011-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="b4011-283">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="b4011-284">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="b4011-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="b4011-285">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="b4011-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="b4011-286">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b4011-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="b4011-287">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="b4011-288">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="b4011-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="b4011-289">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="b4011-290">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b4011-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="b4011-291">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-291">Open a command shell.</span></span> <span data-ttu-id="b4011-292">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="b4011-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="b4011-293">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b4011-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="b4011-294">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-294">Open another command shell instance.</span></span> <span data-ttu-id="b4011-295">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="b4011-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="b4011-296">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="b4011-297">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="b4011-298">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="b4011-299">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4011-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="b4011-300">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="b4011-301">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="b4011-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="b4011-302">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="b4011-303">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="b4011-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="b4011-304">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="b4011-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="b4011-305">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="b4011-306">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="b4011-307">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="b4011-307">The database is ready.</span></span> <span data-ttu-id="b4011-308">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="b4011-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="b4011-309">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b4011-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b4011-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4011-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b4011-311">**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="b4011-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b4011-312">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-313">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-314">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 2.2]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="b4011-315">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-316">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4011-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b4011-317">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="b4011-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="b4011-318">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4011-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b4011-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4011-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b4011-320">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="b4011-321">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="b4011-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="b4011-322">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="b4011-323">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-323">Select **Yes**.</span></span>
1. <span data-ttu-id="b4011-324">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="b4011-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b4011-325">**[統合ターミナル]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="b4011-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="b4011-326">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b4011-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b4011-327">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4011-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b4011-328">バージョン 8.6 より前の Visual Studio for Mac の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[.NET Core]** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="b4011-329">バージョン 8.6 以降の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[Web and Console]\(Web とコンソール)** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="b4011-330">**[ASP.NET Core Web API]** C# プロジェクト テンプレートを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-331">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 2.2]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="b4011-332">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="b4011-333">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="b4011-334">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="b4011-335">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b4011-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="b4011-336">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-336">Add an entity model</span></span>

1. <span data-ttu-id="b4011-337">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="b4011-338">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="b4011-339">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="b4011-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="b4011-340">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="b4011-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="b4011-341">ドキュメントの主キーとしてこのプロパティを指定するために、[`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="b4011-342">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく `string` 型としてパラメーターを渡すことができるようにするために、[`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="b4011-343">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="b4011-344">`BookName` プロパティには、[`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="b4011-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="b4011-345">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="b4011-346">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-346">Add a configuration model</span></span>

1. <span data-ttu-id="b4011-347">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="b4011-348">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="b4011-349">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="b4011-350">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b4011-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="b4011-351">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="b4011-352">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b4011-352">In the preceding code:</span></span>

   * <span data-ttu-id="b4011-353">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="b4011-354">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="b4011-355">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b4011-356">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="b4011-357">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="b4011-358">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="b4011-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="b4011-359">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="b4011-360">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="b4011-361">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="b4011-362">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b4011-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="b4011-363">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="b4011-364">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b4011-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="b4011-365">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="b4011-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="b4011-366">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="b4011-367">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="b4011-368">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="b4011-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm):データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b4011-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="b4011-370">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="b4011-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm):操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="b4011-372">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使用し、特定のコレクション内のデータへのアクセスを取得します。</span><span class="sxs-lookup"><span data-stu-id="b4011-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="b4011-373">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="b4011-374">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b4011-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="b4011-375">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="b4011-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="b4011-376">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="b4011-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="b4011-377">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="b4011-378">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b4011-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="b4011-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm):指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="b4011-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="b4011-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm):指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="b4011-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm):指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="b4011-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="b4011-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm):指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b4011-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b4011-383">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="b4011-383">Add a controller</span></span>

<span data-ttu-id="b4011-384">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b4011-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="b4011-385">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="b4011-385">The preceding web API controller:</span></span>

* <span data-ttu-id="b4011-386">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="b4011-387">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b4011-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="b4011-388">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="b4011-389">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="b4011-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="b4011-390">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="b4011-391">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="b4011-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="b4011-392">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="b4011-392">Test the web API</span></span>

1. <span data-ttu-id="b4011-393">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="b4011-393">Build and run the app.</span></span>

1. <span data-ttu-id="b4011-394">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4011-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="b4011-395">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="b4011-396">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="b4011-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="b4011-397">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="b4011-398">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="b4011-398">Configure JSON serialization options</span></span>

<span data-ttu-id="b4011-399">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="b4011-400">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="b4011-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="b4011-401">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4011-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="b4011-402">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="b4011-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="b4011-403">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="b4011-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="b4011-404">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="b4011-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="b4011-405">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="b4011-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="b4011-406">*Models/Book.cs* では、`BookName` プロパティに次の [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="b4011-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="b4011-407">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="b4011-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="b4011-408">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="b4011-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="b4011-409">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="b4011-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="b4011-410">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b4011-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="b4011-411">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="b4011-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="b4011-412">次の手順</span><span class="sxs-lookup"><span data-stu-id="b4011-412">Next steps</span></span>

<span data-ttu-id="b4011-413">ASP.NET Core Web API の構築について詳しくは、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b4011-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="b4011-414">この記事の YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="b4011-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
