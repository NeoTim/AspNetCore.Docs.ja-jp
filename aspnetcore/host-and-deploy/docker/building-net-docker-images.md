---
title: ASP.NET Core 向けの Docker イメージ
author: tdykstra
description: 公開されている .NET Core Docker イメージを Docker レジストリから使用する方法について説明します。 イメージをプルして独自のイメージをビルドします。
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ea96ae6d36c7e8320ea49e666a807ece72645865
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207791"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="0566b-104">ASP.NET Core 向けの Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="0566b-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="0566b-105">このチュートリアルでは、Docker コンテナー内で ASP.NET Core アプリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0566b-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="0566b-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="0566b-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="0566b-107">Microsoft .NET Core Docker イメージについて学習する</span><span class="sxs-lookup"><span data-stu-id="0566b-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="0566b-108">ASP.NET Core サンプル アプリをダウンロードする</span><span class="sxs-lookup"><span data-stu-id="0566b-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="0566b-109">サンプル アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="0566b-109">Run the sample app locally</span></span>
> * <span data-ttu-id="0566b-110">Linux コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="0566b-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="0566b-111">Windows コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="0566b-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="0566b-112">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="0566b-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="0566b-113">ASP.NET Core の Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="0566b-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="0566b-114">このチュートリアルでは、ASP.NET Core サンプル アプリをダウンロードして、Docker コンテナー内で実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="0566b-115">このサンプルは Linux コンテナーと Windows コンテナーのどちらでも動作します。</span><span class="sxs-lookup"><span data-stu-id="0566b-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="0566b-116">さまざまなコンテナー内でビルドして実行するために、サンプルの Dockerfile では [Docker のマルチステージ ビルド機能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)を使用しています。</span><span class="sxs-lookup"><span data-stu-id="0566b-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="0566b-117">ビルドと実行のコンテナーは、マイクロソフトが Docker Hub に提供しているイメージから作成されます。</span><span class="sxs-lookup"><span data-stu-id="0566b-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="0566b-118">サンプルでは、アプリをビルドするためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="0566b-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="0566b-119">イメージには、コマンド ライン ツール (CLI) が組み込まれた .NET Core SDK が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0566b-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="0566b-120">イメージはローカル開発、デバッグ、および単体テスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="0566b-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="0566b-121">開発とコンパイルのためにツールがインストールされているため、これは比較的大きなイメージになっています。</span><span class="sxs-lookup"><span data-stu-id="0566b-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="0566b-122">サンプルでは、アプリを実行するためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="0566b-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="0566b-123">イメージには ASP.NET Core ランタイムとライブラリが含まれており、実稼働環境でアプリを実行するために最適化されています。</span><span class="sxs-lookup"><span data-stu-id="0566b-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="0566b-124">デプロイとアプリ起動の速度に対応した設計になっており、Docker レジストリから Docker ホストへのネットワーク パフォーマンスが最適化されていることから、イメージは比較的小さいです。</span><span class="sxs-lookup"><span data-stu-id="0566b-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="0566b-125">アプリの実行に必要なバイナリとコンテンツのみが、コンテナーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="0566b-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="0566b-126">コンテンツは実行できる状態になっており、`Docker run` からアプリの起動までを最速で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0566b-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="0566b-127">動的コード コンパイルは Docker モデルで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="0566b-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0566b-128">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0566b-128">Prerequisites</span></span>

* [<span data-ttu-id="0566b-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="0566b-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="0566b-130">Docker クライアント 18.03 以降</span><span class="sxs-lookup"><span data-stu-id="0566b-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="0566b-131">Linux ディストリビューション</span><span class="sxs-lookup"><span data-stu-id="0566b-131">Linux distributions</span></span>
    * [<span data-ttu-id="0566b-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="0566b-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="0566b-133">Debian</span><span class="sxs-lookup"><span data-stu-id="0566b-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="0566b-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="0566b-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="0566b-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="0566b-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="0566b-136">macOS</span><span class="sxs-lookup"><span data-stu-id="0566b-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="0566b-137">Windows</span><span class="sxs-lookup"><span data-stu-id="0566b-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="0566b-138">Git</span><span class="sxs-lookup"><span data-stu-id="0566b-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="0566b-139">サンプル アプリ をダウンロードする</span><span class="sxs-lookup"><span data-stu-id="0566b-139">Download the sample app</span></span>

* <span data-ttu-id="0566b-140">[.NET Core の Docker リポジトリ](https://github.com/dotnet/dotnet-docker)を複製して、サンプルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="0566b-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="0566b-141">アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="0566b-141">Run the app locally</span></span>

* <span data-ttu-id="0566b-142">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="0566b-143">次のコマンドを実行し、アプリをビルドしてローカルで実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="0566b-144">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="0566b-145">コマンド プロンプト上で Ctrl +C キーを押して、アプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="0566b-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="0566b-146">Linux コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="0566b-146">Run in a Linux container</span></span>

* <span data-ttu-id="0566b-147">Docker クライアント上で、Linux コンテナーに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="0566b-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="0566b-148">*dotnet-docker/samples/aspnetapp* にある Dockerfile フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="0566b-149">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="0566b-150">`build` コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="0566b-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="0566b-151">イメージに aspnetapp という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0566b-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="0566b-152">現在のフォルダー内にある Dockerfile を探します (末尾にピリオド)。</span><span class="sxs-lookup"><span data-stu-id="0566b-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="0566b-153">実行コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="0566b-153">The run command arguments:</span></span>
  * <span data-ttu-id="0566b-154">擬似端末を割り当てて、接続されていない場合でも開いた状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="0566b-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="0566b-155">(`--interactive --tty` と効果は同じです。)</span><span class="sxs-lookup"><span data-stu-id="0566b-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="0566b-156">コンテナーが存在する場合は、自動的に削除します。</span><span class="sxs-lookup"><span data-stu-id="0566b-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="0566b-157">ローカル コンピューター上のポート 5000 をコンテナー内のポート 80 にマップします。</span><span class="sxs-lookup"><span data-stu-id="0566b-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="0566b-158">コンテナーに aspnetcore_sample という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0566b-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="0566b-159">aspnetapp イメージを指定します。</span><span class="sxs-lookup"><span data-stu-id="0566b-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="0566b-160">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="0566b-161">Windows コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="0566b-161">Run in a Windows container</span></span>

* <span data-ttu-id="0566b-162">Docker クライアント上で、Windows コンテナーに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="0566b-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="0566b-163">`dotnet-docker/samples/aspnetapp` にある Docker ファイルのフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="0566b-164">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="0566b-165">Windows コンテナーの場合、コンテナーの IP アドレスが必要です (`http://localhost:5000` の参照は機能しません)。</span><span class="sxs-lookup"><span data-stu-id="0566b-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="0566b-166">別のコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="0566b-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="0566b-167">`docker ps` を実行して、実行中のコンテナーを表示します。</span><span class="sxs-lookup"><span data-stu-id="0566b-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="0566b-168">"aspnetcore_sample" コンテナーがそこにあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0566b-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="0566b-169">`docker exec aspnetcore_sample ipconfig` を実行して、コンテナーの IP アドレスを表示します。</span><span class="sxs-lookup"><span data-stu-id="0566b-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="0566b-170">コマンドからの出力は、この例のようになります。</span><span class="sxs-lookup"><span data-stu-id="0566b-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="0566b-171">コンテナーの IPv4 アドレス (たとえば、172.29.245.43) をコピーして、ブラウザーのアドレス バーに貼り付けてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="0566b-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="0566b-172">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="0566b-172">Build and deploy manually</span></span>

<span data-ttu-id="0566b-173">一部のシナリオでは、実行時に必要なアプリケーション ファイルにコピーすることで、アプリをコンテナーにデプロイすることを考える場合があります。</span><span class="sxs-lookup"><span data-stu-id="0566b-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="0566b-174">このセクションでは、手動によるデプロイの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0566b-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="0566b-175">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0566b-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="0566b-176">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-176">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="0566b-177">コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="0566b-177">The command arguments:</span></span>
  * <span data-ttu-id="0566b-178">リリース モードでアプリケーションをビルドします (既定はデバッグ モードです)。</span><span class="sxs-lookup"><span data-stu-id="0566b-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="0566b-179">*published* フォルダーにファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0566b-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="0566b-180">アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="0566b-180">Run the application.</span></span>

  * <span data-ttu-id="0566b-181">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="0566b-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="0566b-182">Linux の場合:</span><span class="sxs-lookup"><span data-stu-id="0566b-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="0566b-183">`http://localhost:5000` を参照してホーム ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="0566b-183">Browse to `http://localhost:5000` to see the home page.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="0566b-184">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="0566b-184">The Dockerfile</span></span>

<span data-ttu-id="0566b-185">ここに示すのは、先ほど実行した `docker build` コマンドで使用された Dockerfile です。</span><span class="sxs-lookup"><span data-stu-id="0566b-185">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="0566b-186">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="0566b-186">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="0566b-187">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0566b-187">Additional resources</span></span>

* [<span data-ttu-id="0566b-188">Docker の build コマンド</span><span class="sxs-lookup"><span data-stu-id="0566b-188">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="0566b-189">Docker の run コマンド</span><span class="sxs-lookup"><span data-stu-id="0566b-189">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="0566b-190">[ASP.NET Core の Docker サンプル](https://github.com/dotnet/dotnet-docker) (このチュートリアルで使用されたものです。)</span><span class="sxs-lookup"><span data-stu-id="0566b-190">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="0566b-191">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="0566b-191">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="0566b-192">Visual Studio Docker ツールの使用</span><span class="sxs-lookup"><span data-stu-id="0566b-192">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="0566b-193">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="0566b-193">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="0566b-194">次の手順</span><span class="sxs-lookup"><span data-stu-id="0566b-194">Next steps</span></span>

<span data-ttu-id="0566b-195">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="0566b-195">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="0566b-196">Microsoft .NET Core Docker イメージについて学習しました</span><span class="sxs-lookup"><span data-stu-id="0566b-196">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="0566b-197">ASP.NET Core サンプル アプリをダウンロードしました</span><span class="sxs-lookup"><span data-stu-id="0566b-197">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="0566b-198">サンプル アプリをローカルで実行しました</span><span class="sxs-lookup"><span data-stu-id="0566b-198">Run the sample app locally</span></span>
> * <span data-ttu-id="0566b-199">Linux コンテナー内でサンプル アプリを実行しました</span><span class="sxs-lookup"><span data-stu-id="0566b-199">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="0566b-200">Windows コンテナー内でサンプルを実行しました</span><span class="sxs-lookup"><span data-stu-id="0566b-200">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="0566b-201">手動でビルドしてデプロイしました</span><span class="sxs-lookup"><span data-stu-id="0566b-201">Built and deployed manually</span></span>

<span data-ttu-id="0566b-202">同じアプリを格納している Git リポジトリにも、ドキュメントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0566b-202">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="0566b-203">リポジトリ内にある利用可能なリソースの概要については、[README ファイル](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0566b-203">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="0566b-204">特に、HTTPS を実装する方法について確認してください。</span><span class="sxs-lookup"><span data-stu-id="0566b-204">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="0566b-205">「[Developing ASP.NET Core Applications with Docker over HTTPS (Docker を使用して HTTPS による ASP.NET Core アプリケーションを開発する)](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)」</span><span class="sxs-lookup"><span data-stu-id="0566b-205">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)</span></span>