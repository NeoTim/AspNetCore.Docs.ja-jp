---
title: ASP.NET Core プロジェクトにおける Identity へのカスタムユーザーデータの追加、ダウンロードおよび削除
author: rick-anderson
description: ASP.NET Core プロジェクトにおいて Identity にカスタム ユーザーデータを追加する方法について説明します。 GDPR ごとのデータを削除します。
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 7a67f55da0e685ed3fd5badb30e8be683411a5ae
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653282"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>ASP.NET Core プロジェクトにおける Identity へのカスタム ユーザーデータの追加、ダウンロードおよび削除

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

この記事では、次の方法について説明します。

* ASP.NET Core web アプリにカスタム ユーザー データを追加します。
* カスタムユーザーデータモデルを <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 属性でマークして、自動的にダウンロードおよび削除できるようにします。 データをダウンロードして削除できるようにすると、 [GDPR](xref:security/gdpr)の要件を満たすことができます。

プロジェクト サンプルは、Razor ページ web アプリから作成されますが、手順は ASP.NET Core MVC web アプリと同様。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Razor Web アプリの作成

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、 > [プロジェクト]** の順に選択します。 [ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。
* **ASP.NET Core Web アプリケーション**の選択 > **OK**
* ドロップダウンで**ASP.NET Core 3.0**を選択します。
* **Web アプリケーション**の選択 > **OK**
* プロジェクトをビルドおよび実行します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、 > [プロジェクト]** の順に選択します。 [ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。
* **ASP.NET Core Web アプリケーション**の選択 > **OK**
* ドロップダウンで**ASP.NET Core 2.2**を選択します。
* **Web アプリケーション**の選択 > **OK**
* プロジェクトをビルドおよび実行します。

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Identity のスキャフォールディングの実行

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。
* **[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。
* **[Id の追加]** ダイアログボックスで、次のオプションを選択します。
  * 既存のレイアウト _Layout ファイルを選択し*ます。*
  * オーバーライドする次のファイルを選択します。
    * **アカウント/登録**
    * **アカウント/管理/インデックス**
  * [ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。 型 (プロジェクトの名前が**WebApp1**の場合は**WebApp1Context** ) をそのまま使用します。
  * [ **+** ] ボタンを選択して、新しい**ユーザークラス**を作成します。 型を受け入れます (プロジェクトの名前が**WebApp1**の場合は**WebApp1User** ) >**追加** を使用します。
* **[追加]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (.csproj) ファイルに追加してください。 プロジェクト ディレクトリに、次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

プロジェクト フォルダーでは、Identity scaffolder を実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

[移行、UseAuthentication、および layout](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。

* 移行を作成し、データベースを更新します。
* `UseAuthentication` を `Startup.Configure` に追加します。
* レイアウトファイルに `<partial name="_LoginPartial" />` を追加します。
* アプリをテストします。
  * ユーザーを登録する
  * **[ログアウト]** リンクの横にある新しいユーザー名を選択します。 ウィンドウを拡大またはユーザー名とその他のリンクを表示するナビゲーション バーのアイコンを選択する必要があります。
  * **[Personal Data]** タブを選択します。
  * [Download] \ (**ダウンロード**\) ボタンを選択して、"お持ちの*データ. json*ファイル" を確認します。
  * **[削除]** ボタンをテストします。これにより、ログオンしているユーザーが削除されます。

## <a name="add-custom-user-data-to-the-identity-db"></a>Identity DB へのカスタム ユーザーデータの追加

カスタムプロパティを使用して `IdentityUser` 派生クラスを更新します。 プロジェクトに WebApp1 という名前を付けた場合、ファイルの名前は*Areas/Identity/Data/WebApp1User*になります。 次のコード ファイルを更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

プロパティ[は、次のよう](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)になります。

* *区分/id/ページ/アカウント/管理/削除データ*の `UserManager.Delete`を呼び出すと削除されます。
* ダウンロードされたデータには、[*区分/id/ページ]/[アカウント*]/[管理]/[データの追加]/[データの管理/ダウンロード] ページがあります。

### <a name="update-the-accountmanageindexcshtml-page"></a>Account/Manage/Index.cshtml ページの更新

次の強調表示されたコードを使用して、*区分/id/Pages/Account/Manage/Index. cshtml*の `InputModel` を更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/管理/インデックス*を更新します。

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Account/Register.cshtml ページの更新

次の強調表示されたコードを使用して、[*区分/id/ページ/アカウント/登録*] の `InputModel` を更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

次の強調表示されたマークアップを使用して、*区分/id/ページ/アカウント/登録*を更新します。

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


プロジェクトをビルドする。

### <a name="add-a-migration-for-the-custom-user-data"></a>カスタム ユーザー データのマイグレーションの追加

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio**パッケージマネージャーコンソール**で次のようにします。

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>カスタム ユーザー データの作成、表示、ダウンロード、削除のテスト

アプリをテストします。

* 新しいユーザーを登録します。
* [`/Identity/Account/Manage`] ページでカスタムユーザーデータを表示します。
* `/Identity/Account/Manage/PersonalData` ページからユーザーの個人データをダウンロードして表示します。
