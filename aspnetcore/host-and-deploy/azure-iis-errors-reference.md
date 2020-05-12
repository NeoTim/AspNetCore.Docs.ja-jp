---
title: ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス
author: rick-anderson
description: Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する一般的なエラーを解決する方法について助言を得ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 7b3454fbd891ca26d44125810a10eb3b3c2c3933
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775207"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="69ecc-103">ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス</span><span class="sxs-lookup"><span data-stu-id="69ecc-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="69ecc-104">このトピックでは、一般的なエラーについて説明し、Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する固有のエラーを解決する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-104">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="69ecc-105">一般的なトラブルシューティング ガイダンスについては、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-105">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="69ecc-106">次の情報を収集します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-106">Collect the following information:</span></span>

* <span data-ttu-id="69ecc-107">ブラウザーの動作 (ステータス コードとエラー メッセージ)</span><span class="sxs-lookup"><span data-stu-id="69ecc-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="69ecc-108">アプリケーション イベント ログのエントリ</span><span class="sxs-lookup"><span data-stu-id="69ecc-108">Application Event Log entries</span></span>
  * <span data-ttu-id="69ecc-109">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-109">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="69ecc-110">IIS</span><span class="sxs-lookup"><span data-stu-id="69ecc-110">IIS</span></span>
    1. <span data-ttu-id="69ecc-111">**[Windows]** メニューで **[スタート]** を選択し、「*Event Viewer*」と入力し、**Enter** を押します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="69ecc-112">**イベント ビューアー**が開いたら、サイド バーで **[Windows ログ]** > **[アプリケーション]** の順に展開します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="69ecc-113">ASP.NET Core モジュールの stdout ログ エントリと debug ログ エントリ</span><span class="sxs-lookup"><span data-stu-id="69ecc-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="69ecc-114">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-114">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="69ecc-115">IIS &ndash; ASP.NET Core モジュール トピックの「[ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」セクションと「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」セクションの指示に従います。</span><span class="sxs-lookup"><span data-stu-id="69ecc-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="69ecc-116">エラー情報を次の一般的なエラーと比較します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="69ecc-117">一致が見つかった場合は、トラブルシューティングのアドバイスに従います。</span><span class="sxs-lookup"><span data-stu-id="69ecc-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="69ecc-118">このトピックではすべてのエラーを網羅しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="69ecc-119">ここに記載されていないエラーに遭遇した場合、このトピックの一番下にある **[コンテンツ フィードバック]** ボタンで新しい問題を登録してください。その際、エラーを再現する方法を詳しく教えてください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="69ecc-120">OS のアップグレードによって 32 ビット ASP.NET Core モジュールが削除された</span><span class="sxs-lookup"><span data-stu-id="69ecc-120">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="69ecc-121">**アプリケーション ログ:** モジュール DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-121">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="69ecc-122">このデータはエラーです。</span><span class="sxs-lookup"><span data-stu-id="69ecc-122">The data is the error.</span></span>

<span data-ttu-id="69ecc-123">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-123">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-124">**C:\Windows\SysWOW64\inetsrv** ディレクトリにある OS ファイルでないファイルは、OS アップグレード時に保持されません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-124">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="69ecc-125">OS アップグレードより前に ASP.NET Core モジュールをインストールしていた場合、OS アップグレード後に 32 ビット モードでアプリ プールを実行しようとすると、この問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-125">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="69ecc-126">OS アップグレード後に ASP.NET Core モジュールを修復してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-126">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="69ecc-127">「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-127">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="69ecc-128">インストーラーを実行するときに **[修復]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-128">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="69ecc-129">サイト拡張機能の不足、32 ビット (x86) および 64 ビット (x64) サイト拡張機能がインストールされている、または間違ったプロセス ビットが設定されている</span><span class="sxs-lookup"><span data-stu-id="69ecc-129">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="69ecc-130">"*Azure App Services でホストしているアプリに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="69ecc-130">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="69ecc-131">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-131">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="69ecc-132">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-132">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-133">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-133">Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-134">hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-134">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-135">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-135">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="69ecc-136">アプリケーション '/LM/W3SVC/1416782824/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-136">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="69ecc-137">**ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-137">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-138">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-138">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

* <span data-ttu-id="69ecc-139">**ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-139">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-140">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-140">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="69ecc-141">HRESULT が失敗し、次が返されました:0x8000ffff。</span><span class="sxs-lookup"><span data-stu-id="69ecc-141">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="69ecc-142">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-142">Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-143">互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-143">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-144">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-144">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="69ecc-145">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-145">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-146">プレビュー ランタイムでアプリを実行している場合、アプリのビットとアプリのランタイム バージョンに一致する、32 ビット (x86) **または** 64 ビット (x64) のいずれかのサイト拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-146">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="69ecc-147">**両方の拡張機能や、拡張機能の複数のランタイム バージョンをインストールしないでください。**</span><span class="sxs-lookup"><span data-stu-id="69ecc-147">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="69ecc-148">ASP.NET Core {ランタイム バージョン} (x86) ランタイム</span><span class="sxs-lookup"><span data-stu-id="69ecc-148">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="69ecc-149">ASP.NET Core {ランタイム バージョン} (x64) ランタイム</span><span class="sxs-lookup"><span data-stu-id="69ecc-149">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="69ecc-150">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-150">Restart the app.</span></span> <span data-ttu-id="69ecc-151">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-151">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="69ecc-152">プレビュー ランタイムでアプリを実行していて、32 ビット (x86)、64 ビット (x64) 両方の[サイト拡張機能](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)がインストールされている場合、アプリのビットと一致しないサイト拡張機能をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-152">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="69ecc-153">サイト拡張機能を削除した後、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-153">After removing the site extension, restart the app.</span></span> <span data-ttu-id="69ecc-154">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-154">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="69ecc-155">プレビュー ランタイムでアプリを実行していて、サイト拡張機能とアプリのビットが一致している場合、プレビュー サイト拡張機能の "*ランタイム バージョン*" がアプリのランタイム バージョンと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-155">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="69ecc-156">**[アプリケーション設定]** のアプリの **[プラットフォーム]** がアプリのビットと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-156">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="69ecc-157">詳細については、「<xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-157">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="69ecc-158">x86 アプリが展開されますが、32 ビット アプリに対してアプリ プールは有効になりません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-158">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="69ecc-159">**ブラウザー:** HTTP エラー 500.30 - ANCM インプロセス起動失敗</span><span class="sxs-lookup"><span data-stu-id="69ecc-159">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="69ecc-160">**アプリケーション ログ:** 物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' に予想外のマネージド例外が発生しました、例外コード = '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-160">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="69ecc-161">詳細については、stderr ログを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-161">Please check the stderr logs for more information.</span></span> <span data-ttu-id="69ecc-162">物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' で clr とマネージド アプリケーションを読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-162">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="69ecc-163">CLR ワーカー スレッドが途中で終了しました</span><span class="sxs-lookup"><span data-stu-id="69ecc-163">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="69ecc-164">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="69ecc-164">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

* <span data-ttu-id="69ecc-165">**ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8007023e</span><span class="sxs-lookup"><span data-stu-id="69ecc-165">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="69ecc-166">このシナリオは、自己完結型アプリの公開時、SDK によってトラップされます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-166">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="69ecc-167">RID がプラットフォーム ターゲットに一致しない場合 (`win10-x64` RID とプロジェクト ファイルの `<PlatformTarget>x86</PlatformTarget>` など)、SDK からエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-167">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="69ecc-168">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-168">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-169">x86 フレームワークに依存する展開の場合 (`<PlatformTarget>x86</PlatformTarget>`)、32 ビット アプリに対して IIS アプリ プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-169">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="69ecc-170">IIS Manager でアプリ プールの **[詳細設定]** を開き、 **[32 ビット アプリケーションの有効化]** を **[True]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-170">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="69ecc-171">プラットフォームが RID と競合している</span><span class="sxs-lookup"><span data-stu-id="69ecc-171">Platform conflicts with RID</span></span>

* <span data-ttu-id="69ecc-172">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-172">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="69ecc-173">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' でプロセスを開始できませんでした、エラー コード = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="69ecc-173">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="69ecc-174">**ASP.NET Core モジュールの stdout ログ:** 未処理の例外:System.BadImageFormatException:ファイルまたはアセンブリ '{ASSEMBLY}.dll' を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-174">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="69ecc-175">正しくない形式のプログラムを読み込もうとしました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-175">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="69ecc-176">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-176">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-177">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-177">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-178">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-178">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-179">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-179">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-180">Azure Apps 展開で、アプリケーションをアップグレードして新しいアセンブリを展開しようとしたときにこの例外が発生した場合は、以前の展開からすべてのファイルを手動で削除してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-180">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="69ecc-181">アップグレードしたアプリを展開するとき、互換性のないアセンブリが残っていると、`System.BadImageFormatException` 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-181">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="69ecc-182">URI のエンドポイントが間違っているか、Web サイトが停止している</span><span class="sxs-lookup"><span data-stu-id="69ecc-182">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="69ecc-183">**ブラウザー:** ERR_CONNECTION_REFUSED **--または--** 接続できません</span><span class="sxs-lookup"><span data-stu-id="69ecc-183">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="69ecc-184">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-184">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-185">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-185">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-186">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-186">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-187">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-187">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-188">アプリに対して正しい URI エンドポイントが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-188">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="69ecc-189">バインドを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-189">Check the bindings.</span></span>

* <span data-ttu-id="69ecc-190">IIS Web サイトが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-190">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="69ecc-191">CoreWebEngine または W3SVC サーバー機能が無効</span><span class="sxs-lookup"><span data-stu-id="69ecc-191">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="69ecc-192">**OS の例外:** ASP.NET Core モジュールを使用するには、IIS 7.0 CoreWebEngine および W3SVC の機能をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-192">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="69ecc-193">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-193">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-194">適切な役割と機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-194">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="69ecc-195">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-195">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="69ecc-196">Web サイト物理パスが間違っているか、アプリが見つからない</span><span class="sxs-lookup"><span data-stu-id="69ecc-196">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="69ecc-197">**ブラウザー:** 403 許可されていません - アクセスが拒否されました **--または--** 403.14 許可されていません - Web サーバーは、このディレクトリの内容の一覧を表示しないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="69ecc-197">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="69ecc-198">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-198">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-199">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-199">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-200">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-200">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-201">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-201">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-202">IIS Web サイトの**基本設定**と物理アプリのフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-202">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="69ecc-203">アプリが IIS Web サイトの**物理パス**にあるフォルダー内に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-203">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="69ecc-204">役割が正しくない、ASP.NET Core モジュールがインストールされていない、または不適切なアクセス許可</span><span class="sxs-lookup"><span data-stu-id="69ecc-204">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="69ecc-205">**ブラウザー:** 500.19 内部サーバー エラー - ページに関連する構成データが無効であるため、要求されたページにアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-205">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="69ecc-206">**--または--** このページを表示できません</span><span class="sxs-lookup"><span data-stu-id="69ecc-206">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="69ecc-207">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-207">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-208">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-208">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-209">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-209">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-210">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-210">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-211">適切な役割が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-211">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="69ecc-212">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-212">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="69ecc-213">**[プログラムと機能]** または **[アプリと機能]** を開き、 **[Windows Server Hosting]** がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-213">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="69ecc-214">インストールされているプログラムの一覧に **[Windows Server Hosting]** がない場合、.NET Core ホスティング バンドルをダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-214">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="69ecc-215">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="69ecc-215">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="69ecc-216">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-216">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="69ecc-217">**[アプリケーション プール]** > **[プロセス モデル]** > **[Identity]** が **ApplicationPoolIdentity** に設定されていることを確認します。または、カスタム ID にアプリの展開フォルダーにアクセスするための正しいアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-217">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="69ecc-218">ASP.NET Core ホスティング バンドルをアンインストールし、以前のバージョンのホスティング バンドルをインストールした場合、*applicationHost.config* ファイルには ASP.NET Core モジュールのセクションが含まれません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-218">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="69ecc-219">*applicationHost.config* で *%windir%/System32/inetsrv/config* を開き、`<configuration><configSections><sectionGroup name="system.webServer">` セクション グループを見つけます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-219">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="69ecc-220">セクション グループに ASP.NET Core モジュールのセクションがない場合は、セクション要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-220">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="69ecc-221">または、ASP.NET Core ホスティング バンドルの最新バージョンをインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-221">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="69ecc-222">最新バージョンは、ポートされている ASP.NET Core アプリと下位互換性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-222">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="69ecc-223">processPath の誤り、PATH 変数の欠如、ホスティング バンドルが未インストール、システムまたは IIS が再起動されていない、VC++ 再頒布可能パッケージが未インストール、dotnet.exe アクセス違反</span><span class="sxs-lookup"><span data-stu-id="69ecc-223">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="69ecc-224">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-224">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="69ecc-225">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}"</span><span class="sxs-lookup"><span data-stu-id="69ecc-225">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="69ecc-226">' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="69ecc-226">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="69ecc-227">アプリケーション '{PATH}' は開始できませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-227">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="69ecc-228">実行可能ファイルが '{PATH}' で見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-228">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="69ecc-229">アプリケーション '/LM/W3SVC/2/ROOT' を起動できませんでした、エラー コード '0x8007023e'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-229">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="69ecc-230">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-230">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-231">**ASP.NET Core モジュール デバッグ ログ:** イベント ログ:'アプリケーション '{PATH}' を起動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-231">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="69ecc-232">実行可能ファイルが '{PATH}' で見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-232">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="69ecc-233">HRESULT が失敗し、次が返されました:0x8007023e</span><span class="sxs-lookup"><span data-stu-id="69ecc-233">Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="69ecc-234">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-234">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-235">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-235">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-236">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-236">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-237">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-238">*web.config* で `<aspNetCore>` 要素の *processPath* 属性を調べ、フレームワークに依存する展開 (FDD) の場合はそれが `dotnet` であること、[自己完結型展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) の場合はそれが `.\{ASSEMBLY}.exe` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-238">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="69ecc-239">FDD の場合、PATH 設定で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-239">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="69ecc-240">*C:\Program Files\dotnet\\* がシステムの PATH 設定に含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-240">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="69ecc-241">FDD では、アプリ プールのユーザー ID で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-241">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="69ecc-242">アプリ プール ユーザー ID に、*C:\Program Files\dotnet* ディレクトリへのアクセス許可が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-242">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="69ecc-243">*C:\Program Files\dotnet* とアプリのディレクトリに、アプリ プール ユーザー ID に対する拒否ルールが構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-243">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="69ecc-244">FDD を配置し、IIS を再起動せずに .NET Core をインストールした可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-244">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="69ecc-245">サーバーを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-245">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="69ecc-246">ホスト システムで、 .NET Core ランタイムをインストールせずに、FDD を配置した可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-246">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="69ecc-247">.NET Core ランタイムがインストールされていない場合は、システムで **.NET Core ホスティング バンドルのインストーラー**を実行します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-247">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="69ecc-248">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="69ecc-248">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="69ecc-249">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-249">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="69ecc-250">特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-250">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="69ecc-251">インストールを完了するために、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-251">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="69ecc-252">\<aspNetCore> 要素の引数の誤り</span><span class="sxs-lookup"><span data-stu-id="69ecc-252">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="69ecc-253">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-253">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="69ecc-254">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-254">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-255">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-255">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="69ecc-256">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-256">Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-257">hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="69ecc-257">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="69ecc-258">dotnet SDK を次の場所からインストールしてください。 https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 アプリケーション '/LM/W3SVC/3/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-258">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="69ecc-259">**ASP.NET Core モジュールの stdout ログ:** dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="69ecc-259">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="69ecc-260">dotnet SDK を https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 からインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-260">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="69ecc-261">**ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-261">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-262">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-262">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="69ecc-263">HRESULT が失敗し、次が返されました:0x8000ffff インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-263">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-264">hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="69ecc-264">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="69ecc-265">dotnet SDK を次の場所からインストールしてください。 https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT が失敗し、次が返されました:0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="69ecc-265">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="69ecc-266">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-266">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-267">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-267">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-268">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-268">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-269">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-269">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-270">*web.config* で `<aspNetCore>` 要素の *arguments* 属性を調べ、次のいずれかになっていることを確認します。(a) フレームワークに依存する展開 (FDD) の場合は `.\{ASSEMBLY}.dll`、または (b) 自己完結型の展開 (SCD) の場合は、未指定の空の文字列 (`arguments=""`) か、アプリの引数のリスト (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-270">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="69ecc-271">.NET Core 共有フレームワークがありません</span><span class="sxs-lookup"><span data-stu-id="69ecc-271">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="69ecc-272">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-272">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="69ecc-273">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-273">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-274">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-274">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="69ecc-275">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-275">Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-276">hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-276">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-277">指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-277">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="69ecc-278">アプリケーション '/LM/W3SVC/5/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-278">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="69ecc-279">**ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-279">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-280">指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-280">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="69ecc-281">**ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="69ecc-281">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="69ecc-282">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-282">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-283">フレームワークに依存する展開 (FDD) では、正しいランタイムがシステムにインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-283">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="69ecc-284">アプリケーション プールの停止</span><span class="sxs-lookup"><span data-stu-id="69ecc-284">Stopped Application Pool</span></span>

* <span data-ttu-id="69ecc-285">**ブラウザー:** 503 サービスをご利用いただけません</span><span class="sxs-lookup"><span data-stu-id="69ecc-285">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="69ecc-286">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-286">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-287">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-287">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-288">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-288">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-289">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-289">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-290">アプリケーション プールが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-290">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="69ecc-291">サブアプリケーションに \<handlers> セクションが含まれている</span><span class="sxs-lookup"><span data-stu-id="69ecc-291">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="69ecc-292">**ブラウザー:** HTTP エラー 500.19 - 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-292">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="69ecc-293">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-293">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-294">**ASP.NET Core モジュールの stdout ログ:** ルート アプリのログ ファイルが作成され、通常の操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="69ecc-294">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="69ecc-295">サブアプリのログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-295">The sub-app's log file isn't created.</span></span>

* <span data-ttu-id="69ecc-296">**ASP.NET Core モジュール デバッグ ログ:** ルート アプリのログ ファイルが作成され、通常の動作を示します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-296">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="69ecc-297">サブアプリのログ ファイルが作成されません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-297">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="69ecc-298">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-298">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-299">サブアプリの *web.config* ファイルに `<handlers>` セクションがないか、サブアプリが親アプリのハンドラーを継承していないことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-299">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="69ecc-300">*web.config* の親アプリの `<system.webServer>` セクションが `<location>` 要素の中に置かれています。</span><span class="sxs-lookup"><span data-stu-id="69ecc-300">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="69ecc-301"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、親アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-301">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="69ecc-302">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-302">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="69ecc-303">stdout ログのパスが正しくありません</span><span class="sxs-lookup"><span data-stu-id="69ecc-303">stdout log path incorrect</span></span>

* <span data-ttu-id="69ecc-304">**ブラウザー:** アプリは通常どおりに応答します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-304">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="69ecc-305">**アプリケーション ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-305">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="69ecc-306">例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-306">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="69ecc-307">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-307">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="69ecc-308">例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-308">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="69ecc-309">{PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-309">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="69ecc-310">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-310">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="69ecc-311">**ASP.NET Core モジュール デバッグ ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-311">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="69ecc-312">例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-312">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="69ecc-313">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-313">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="69ecc-314">例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-314">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="69ecc-315">{PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-315">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

<span data-ttu-id="69ecc-316">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-316">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-317">*web.config* の `<aspNetCore>` 要素で指定された `stdoutLogFile` パスが存在しません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-317">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="69ecc-318">詳細については、「[ASP.NET Core モジュール:ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-318">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="69ecc-319">アプリ プール ユーザーに stdout ログ パスに対する書き込みアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-319">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="69ecc-320">アプリケーション構成の一般的な問題</span><span class="sxs-lookup"><span data-stu-id="69ecc-320">Application configuration general issue</span></span>

* <span data-ttu-id="69ecc-321">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス要求ハンドラー失敗 **--または--** HTTP エラー 500.30 - ANCM インプロセス起動失敗</span><span class="sxs-lookup"><span data-stu-id="69ecc-321">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="69ecc-322">**アプリケーション ログ:** 変数</span><span class="sxs-lookup"><span data-stu-id="69ecc-322">**Application Log:** Variable</span></span>

* <span data-ttu-id="69ecc-323">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されますが空です。あるいは作成され、通常のエントリが含まれますが、アプリのところでエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-323">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="69ecc-324">**ASP.NET Core モジュール デバッグ ログ:** 変数</span><span class="sxs-lookup"><span data-stu-id="69ecc-324">**ASP.NET Core Module Debug Log:** Variable</span></span>

<span data-ttu-id="69ecc-325">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-325">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-326">このプロセスはおそらく、アプリの設定またはプログラミングに問題があり、開始できませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-326">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="69ecc-327">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-327">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="69ecc-328">このトピックでは、一般的なエラーについて説明し、Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する固有のエラーを解決する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-328">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="69ecc-329">一般的なトラブルシューティング ガイダンスについては、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-329">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="69ecc-330">次の情報を収集します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-330">Collect the following information:</span></span>

* <span data-ttu-id="69ecc-331">ブラウザーの動作 (ステータス コードとエラー メッセージ)</span><span class="sxs-lookup"><span data-stu-id="69ecc-331">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="69ecc-332">アプリケーション イベント ログのエントリ</span><span class="sxs-lookup"><span data-stu-id="69ecc-332">Application Event Log entries</span></span>
  * <span data-ttu-id="69ecc-333">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-333">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="69ecc-334">IIS</span><span class="sxs-lookup"><span data-stu-id="69ecc-334">IIS</span></span>
    1. <span data-ttu-id="69ecc-335">**[Windows]** メニューで **[スタート]** を選択し、「*Event Viewer*」と入力し、**Enter** を押します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-335">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="69ecc-336">**イベント ビューアー**が開いたら、サイド バーで **[Windows ログ]** > **[アプリケーション]** の順に展開します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-336">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="69ecc-337">ASP.NET Core モジュールの stdout ログ エントリと debug ログ エントリ</span><span class="sxs-lookup"><span data-stu-id="69ecc-337">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="69ecc-338">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-338">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="69ecc-339">IIS &ndash; ASP.NET Core モジュール トピックの「[ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」セクションと「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」セクションの指示に従います。</span><span class="sxs-lookup"><span data-stu-id="69ecc-339">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="69ecc-340">エラー情報を次の一般的なエラーと比較します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-340">Compare error information to the following common errors.</span></span> <span data-ttu-id="69ecc-341">一致が見つかった場合は、トラブルシューティングのアドバイスに従います。</span><span class="sxs-lookup"><span data-stu-id="69ecc-341">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="69ecc-342">このトピックではすべてのエラーを網羅しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-342">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="69ecc-343">ここに記載されていないエラーに遭遇した場合、このトピックの一番下にある **[コンテンツ フィードバック]** ボタンで新しい問題を登録してください。その際、エラーを再現する方法を詳しく教えてください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-343">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="69ecc-344">OS のアップグレードによって 32 ビット ASP.NET Core モジュールが削除された</span><span class="sxs-lookup"><span data-stu-id="69ecc-344">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="69ecc-345">**アプリケーション ログ:** モジュール DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-345">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="69ecc-346">このデータはエラーです。</span><span class="sxs-lookup"><span data-stu-id="69ecc-346">The data is the error.</span></span>

<span data-ttu-id="69ecc-347">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-347">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-348">**C:\Windows\SysWOW64\inetsrv** ディレクトリにある OS ファイルでないファイルは、OS アップグレード時に保持されません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-348">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="69ecc-349">OS アップグレードより前に ASP.NET Core モジュールをインストールしていた場合、OS アップグレード後に 32 ビット モードでアプリ プールを実行しようとすると、この問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-349">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="69ecc-350">OS アップグレード後に ASP.NET Core モジュールを修復してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-350">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="69ecc-351">「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-351">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="69ecc-352">インストーラーを実行するときに **[修復]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-352">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="69ecc-353">サイト拡張機能の不足、32 ビット (x86) および 64 ビット (x64) サイト拡張機能がインストールされている、または間違ったプロセス ビットが設定されている</span><span class="sxs-lookup"><span data-stu-id="69ecc-353">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="69ecc-354">"*Azure App Services でホストしているアプリに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="69ecc-354">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="69ecc-355">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-355">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="69ecc-356">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-356">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="69ecc-357">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-357">Could not find inprocess request handler.</span></span> <span data-ttu-id="69ecc-358">hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-358">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-359">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-359">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="69ecc-360">アプリケーション '/LM/W3SVC/1416782824/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-360">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="69ecc-361">**ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-361">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="69ecc-362">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-362">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="69ecc-363">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-363">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-364">プレビュー ランタイムでアプリを実行している場合、アプリのビットとアプリのランタイム バージョンに一致する、32 ビット (x86) **または** 64 ビット (x64) のいずれかのサイト拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-364">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="69ecc-365">**両方の拡張機能や、拡張機能の複数のランタイム バージョンをインストールしないでください。**</span><span class="sxs-lookup"><span data-stu-id="69ecc-365">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="69ecc-366">ASP.NET Core {ランタイム バージョン} (x86) ランタイム</span><span class="sxs-lookup"><span data-stu-id="69ecc-366">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="69ecc-367">ASP.NET Core {ランタイム バージョン} (x64) ランタイム</span><span class="sxs-lookup"><span data-stu-id="69ecc-367">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="69ecc-368">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-368">Restart the app.</span></span> <span data-ttu-id="69ecc-369">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-369">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="69ecc-370">プレビュー ランタイムでアプリを実行していて、32 ビット (x86)、64 ビット (x64) 両方の[サイト拡張機能](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)がインストールされている場合、アプリのビットと一致しないサイト拡張機能をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-370">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="69ecc-371">サイト拡張機能を削除した後、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-371">After removing the site extension, restart the app.</span></span> <span data-ttu-id="69ecc-372">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-372">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="69ecc-373">プレビュー ランタイムでアプリを実行していて、サイト拡張機能とアプリのビットが一致している場合、プレビュー サイト拡張機能の "*ランタイム バージョン*" がアプリのランタイム バージョンと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-373">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="69ecc-374">**[アプリケーション設定]** のアプリの **[プラットフォーム]** がアプリのビットと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-374">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="69ecc-375">詳細については、「<xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-375">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="69ecc-376">x86 アプリが展開されますが、32 ビット アプリに対してアプリ プールは有効になりません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-376">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="69ecc-377">**ブラウザー:** HTTP エラー 500.30 - ANCM インプロセス起動失敗</span><span class="sxs-lookup"><span data-stu-id="69ecc-377">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="69ecc-378">**アプリケーション ログ:** 物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' に予想外のマネージド例外が発生しました、例外コード = '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="69ecc-378">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="69ecc-379">詳細については、stderr ログを確認してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-379">Please check the stderr logs for more information.</span></span> <span data-ttu-id="69ecc-380">物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' で clr とマネージド アプリケーションを読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-380">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="69ecc-381">CLR ワーカー スレッドが途中で終了しました</span><span class="sxs-lookup"><span data-stu-id="69ecc-381">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="69ecc-382">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="69ecc-382">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="69ecc-383">このシナリオは、自己完結型アプリの公開時、SDK によってトラップされます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-383">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="69ecc-384">RID がプラットフォーム ターゲットに一致しない場合 (`win10-x64` RID とプロジェクト ファイルの `<PlatformTarget>x86</PlatformTarget>` など)、SDK からエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-384">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="69ecc-385">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-385">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-386">x86 フレームワークに依存する展開の場合 (`<PlatformTarget>x86</PlatformTarget>`)、32 ビット アプリに対して IIS アプリ プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-386">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="69ecc-387">IIS Manager でアプリ プールの **[詳細設定]** を開き、 **[32 ビット アプリケーションの有効化]** を **[True]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-387">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="69ecc-388">プラットフォームが RID と競合している</span><span class="sxs-lookup"><span data-stu-id="69ecc-388">Platform conflicts with RID</span></span>

* <span data-ttu-id="69ecc-389">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-389">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="69ecc-390">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' でプロセスを開始できませんでした、エラー コード = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="69ecc-390">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="69ecc-391">**ASP.NET Core モジュールの stdout ログ:** 未処理の例外:System.BadImageFormatException:ファイルまたはアセンブリ '{ASSEMBLY}.dll' を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-391">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="69ecc-392">正しくない形式のプログラムを読み込もうとしました。</span><span class="sxs-lookup"><span data-stu-id="69ecc-392">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="69ecc-393">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-393">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-394">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-394">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-395">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-395">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-396">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-396">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-397">Azure Apps 展開で、アプリケーションをアップグレードして新しいアセンブリを展開しようとしたときにこの例外が発生した場合は、以前の展開からすべてのファイルを手動で削除してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-397">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="69ecc-398">アップグレードしたアプリを展開するとき、互換性のないアセンブリが残っていると、`System.BadImageFormatException` 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-398">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="69ecc-399">URI のエンドポイントが間違っているか、Web サイトが停止している</span><span class="sxs-lookup"><span data-stu-id="69ecc-399">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="69ecc-400">**ブラウザー:** ERR_CONNECTION_REFUSED **--または--** 接続できません</span><span class="sxs-lookup"><span data-stu-id="69ecc-400">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="69ecc-401">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-401">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-402">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-402">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-403">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-403">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-404">アプリに対して正しい URI エンドポイントが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-404">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="69ecc-405">バインドを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-405">Check the bindings.</span></span>

* <span data-ttu-id="69ecc-406">IIS Web サイトが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-406">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="69ecc-407">CoreWebEngine または W3SVC サーバー機能が無効</span><span class="sxs-lookup"><span data-stu-id="69ecc-407">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="69ecc-408">**OS の例外:** ASP.NET Core モジュールを使用するには、IIS 7.0 CoreWebEngine および W3SVC の機能をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-408">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="69ecc-409">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-409">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-410">適切な役割と機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-410">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="69ecc-411">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-411">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="69ecc-412">Web サイト物理パスが間違っているか、アプリが見つからない</span><span class="sxs-lookup"><span data-stu-id="69ecc-412">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="69ecc-413">**ブラウザー:** 403 許可されていません - アクセスが拒否されました **--または--** 403.14 許可されていません - Web サーバーは、このディレクトリの内容の一覧を表示しないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="69ecc-413">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="69ecc-414">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-414">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-415">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-415">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-416">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-416">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-417">IIS Web サイトの**基本設定**と物理アプリのフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-417">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="69ecc-418">アプリが IIS Web サイトの**物理パス**にあるフォルダー内に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-418">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="69ecc-419">役割が正しくない、ASP.NET Core モジュールがインストールされていない、または不適切なアクセス許可</span><span class="sxs-lookup"><span data-stu-id="69ecc-419">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="69ecc-420">**ブラウザー:** 500.19 内部サーバー エラー - ページに関連する構成データが無効であるため、要求されたページにアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-420">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="69ecc-421">**--または--** このページを表示できません</span><span class="sxs-lookup"><span data-stu-id="69ecc-421">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="69ecc-422">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-422">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-423">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-423">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-424">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-424">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-425">適切な役割が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-425">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="69ecc-426">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-426">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="69ecc-427">**[プログラムと機能]** または **[アプリと機能]** を開き、 **[Windows Server Hosting]** がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-427">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="69ecc-428">インストールされているプログラムの一覧に **[Windows Server Hosting]** がない場合、.NET Core ホスティング バンドルをダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-428">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="69ecc-429">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="69ecc-429">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="69ecc-430">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-430">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="69ecc-431">**[アプリケーション プール]** > **[プロセス モデル]** > **[Identity]** が **ApplicationPoolIdentity** に設定されていることを確認します。または、カスタム ID にアプリの展開フォルダーにアクセスするための正しいアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-431">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="69ecc-432">ASP.NET Core ホスティング バンドルをアンインストールし、以前のバージョンのホスティング バンドルをインストールした場合、*applicationHost.config* ファイルには ASP.NET Core モジュールのセクションが含まれません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-432">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="69ecc-433">*applicationHost.config* で *%windir%/System32/inetsrv/config* を開き、`<configuration><configSections><sectionGroup name="system.webServer">` セクション グループを見つけます。</span><span class="sxs-lookup"><span data-stu-id="69ecc-433">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="69ecc-434">セクション グループに ASP.NET Core モジュールのセクションがない場合は、セクション要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-434">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="69ecc-435">または、ASP.NET Core ホスティング バンドルの最新バージョンをインストールします。</span><span class="sxs-lookup"><span data-stu-id="69ecc-435">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="69ecc-436">最新バージョンは、ポートされている ASP.NET Core アプリと下位互換性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-436">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="69ecc-437">processPath の誤り、PATH 変数の欠如、ホスティング バンドルが未インストール、システムまたは IIS が再起動されていない、VC++ 再頒布可能パッケージが未インストール、dotnet.exe アクセス違反</span><span class="sxs-lookup"><span data-stu-id="69ecc-437">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="69ecc-438">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-438">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="69ecc-439">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}"</span><span class="sxs-lookup"><span data-stu-id="69ecc-439">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="69ecc-440">' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="69ecc-440">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="69ecc-441">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="69ecc-441">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="69ecc-442">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-442">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-443">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-443">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-444">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-444">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-445">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-445">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-446">*web.config* で `<aspNetCore>` 要素の *processPath* 属性を調べ、フレームワークに依存する展開 (FDD) の場合はそれが `dotnet` であること、[自己完結型展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) の場合はそれが `.\{ASSEMBLY}.exe` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-446">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="69ecc-447">FDD の場合、PATH 設定で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-447">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="69ecc-448">*C:\Program Files\dotnet\\* がシステムの PATH 設定に含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-448">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="69ecc-449">FDD では、アプリ プールのユーザー ID で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-449">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="69ecc-450">アプリ プール ユーザー ID に、*C:\Program Files\dotnet* ディレクトリへのアクセス許可が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-450">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="69ecc-451">*C:\Program Files\dotnet* とアプリのディレクトリに、アプリ プール ユーザー ID に対する拒否ルールが構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-451">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="69ecc-452">FDD を配置し、IIS を再起動せずに .NET Core をインストールした可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-452">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="69ecc-453">サーバーを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-453">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="69ecc-454">ホスト システムで、 .NET Core ランタイムをインストールせずに、FDD を配置した可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-454">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="69ecc-455">.NET Core ランタイムがインストールされていない場合は、システムで **.NET Core ホスティング バンドルのインストーラー**を実行します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-455">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="69ecc-456">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="69ecc-456">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="69ecc-457">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-457">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="69ecc-458">特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-458">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="69ecc-459">インストールを完了するために、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-459">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="69ecc-460">\<aspNetCore> 要素の引数の誤り</span><span class="sxs-lookup"><span data-stu-id="69ecc-460">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="69ecc-461">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-461">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="69ecc-462">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"dotnet" .\{ASSEMBLY}.dll' でプロセスを開始できませんでした、エラー コード = '0x80004005 : 80008081。</span><span class="sxs-lookup"><span data-stu-id="69ecc-462">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="69ecc-463">**ASP.NET Core モジュールの stdout ログ:** 実行するアプリケーションが存在しません。'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="69ecc-463">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

<span data-ttu-id="69ecc-464">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-464">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-465">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-465">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="69ecc-466">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69ecc-466">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="69ecc-467">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-467">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="69ecc-468">*web.config* で `<aspNetCore>` 要素の *arguments* 属性を調べ、次のいずれかになっていることを確認します。(a) フレームワークに依存する展開 (FDD) の場合は `.\{ASSEMBLY}.dll`、または (b) 自己完結型の展開 (SCD) の場合は、未指定の空の文字列 (`arguments=""`) か、アプリの引数のリスト (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`)。</span><span class="sxs-lookup"><span data-stu-id="69ecc-468">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

<span data-ttu-id="69ecc-469">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-469">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-470">フレームワークに依存する展開 (FDD) では、正しいランタイムがシステムにインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-470">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="69ecc-471">アプリケーション プールの停止</span><span class="sxs-lookup"><span data-stu-id="69ecc-471">Stopped Application Pool</span></span>

* <span data-ttu-id="69ecc-472">**ブラウザー:** 503 サービスをご利用いただけません</span><span class="sxs-lookup"><span data-stu-id="69ecc-472">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="69ecc-473">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-473">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-474">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-474">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-475">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-475">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-476">アプリケーション プールが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-476">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="69ecc-477">サブアプリケーションに \<handlers> セクションが含まれている</span><span class="sxs-lookup"><span data-stu-id="69ecc-477">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="69ecc-478">**ブラウザー:** HTTP エラー 500.19 - 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-478">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="69ecc-479">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="69ecc-479">**Application Log:** No entry</span></span>

* <span data-ttu-id="69ecc-480">**ASP.NET Core モジュールの stdout ログ:** ルート アプリのログ ファイルが作成され、通常の操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="69ecc-480">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="69ecc-481">サブアプリのログ ファイルが作成されません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-481">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="69ecc-482">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-482">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-483">サブアプリの *web.config* ファイルに `<handlers>` セクションが含まれていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-483">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="69ecc-484">stdout ログのパスが正しくありません</span><span class="sxs-lookup"><span data-stu-id="69ecc-484">stdout log path incorrect</span></span>

* <span data-ttu-id="69ecc-485">**ブラウザー:** アプリは通常どおりに応答します。</span><span class="sxs-lookup"><span data-stu-id="69ecc-485">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="69ecc-486">**アプリケーション ログ:** 警告 :stdout ログ ファイル \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log を作成できません、エラー コード = -2147024893。</span><span class="sxs-lookup"><span data-stu-id="69ecc-486">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="69ecc-487">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-487">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="69ecc-488">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-488">Troubleshooting:</span></span>

* <span data-ttu-id="69ecc-489">*web.config* の `<aspNetCore>` 要素で指定された `stdoutLogFile` パスが存在しません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-489">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="69ecc-490">詳細については、「[ASP.NET Core モジュール:ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-490">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="69ecc-491">アプリ プール ユーザーに stdout ログ パスに対する書き込みアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="69ecc-491">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="69ecc-492">アプリケーション構成の一般的な問題</span><span class="sxs-lookup"><span data-stu-id="69ecc-492">Application configuration general issue</span></span>

* <span data-ttu-id="69ecc-493">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="69ecc-493">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="69ecc-494">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' でプロセスを作成しましたが、クラッシュしたか、応答しなかったか、所与のポート '{PORT}' で待機しませんでした、エラー コード = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="69ecc-494">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="69ecc-495">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="69ecc-495">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="69ecc-496">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="69ecc-496">Troubleshooting:</span></span>

<span data-ttu-id="69ecc-497">このプロセスはおそらく、アプリの設定またはプログラミングに問題があり、開始できませんでした。</span><span class="sxs-lookup"><span data-stu-id="69ecc-497">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="69ecc-498">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69ecc-498">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
