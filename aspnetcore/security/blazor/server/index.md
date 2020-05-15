---
title: ASP.NET Core Blazor サーバー アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: bbd8b6fcd357b8929bf097450854d98fbea2570e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772636"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="af6f8-103">ASP.NET Core Blazor サーバー アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="af6f8-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="af6f8-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="af6f8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="af6f8-105">Blazor サーバー プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="af6f8-105">Blazor Server project template</span></span>

<span data-ttu-id="af6f8-106">Blazor サーバー プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af6f8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af6f8-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="af6f8-108">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="af6f8-109">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="af6f8-110">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="af6f8-111">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="af6f8-111">**No Authentication**</span></span>
* <span data-ttu-id="af6f8-112">**個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="af6f8-113">ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="af6f8-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="af6f8-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="af6f8-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="af6f8-115">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="af6f8-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="af6f8-116">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="af6f8-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af6f8-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af6f8-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="af6f8-118">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="af6f8-119">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="af6f8-120">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="af6f8-120">Authentication mechanism</span></span> | <span data-ttu-id="af6f8-121">説明</span><span class="sxs-lookup"><span data-stu-id="af6f8-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="af6f8-122">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="af6f8-122">`None` (default)</span></span>         | <span data-ttu-id="af6f8-123">認証なし</span><span class="sxs-lookup"><span data-stu-id="af6f8-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="af6f8-124">ASP.NET Core ID を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="af6f8-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="af6f8-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="af6f8-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="af6f8-126">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="af6f8-127">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="af6f8-128">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-128">Windows Authentication</span></span> |

<span data-ttu-id="af6f8-129">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="af6f8-130">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-130">Create a folder for the project.</span></span>
* <span data-ttu-id="af6f8-131">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-131">Name the project.</span></span>

<span data-ttu-id="af6f8-132">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af6f8-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="af6f8-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af6f8-134"><xref:blazor/get-started> の記事に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="af6f8-135">**[Configure your new Blazor Server App]\(新しい Blazor サーバー アプリの構成\)** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="af6f8-136">ASP.NET Core ID を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af6f8-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="af6f8-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af6f8-138">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事に記載されている .NET Core CLI のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="af6f8-139">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="af6f8-140">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="af6f8-140">Authentication mechanism</span></span> | <span data-ttu-id="af6f8-141">説明</span><span class="sxs-lookup"><span data-stu-id="af6f8-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="af6f8-142">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="af6f8-142">`None` (default)</span></span>         | <span data-ttu-id="af6f8-143">認証なし</span><span class="sxs-lookup"><span data-stu-id="af6f8-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="af6f8-144">ASP.NET Core ID を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="af6f8-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="af6f8-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="af6f8-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="af6f8-146">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="af6f8-147">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="af6f8-148">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="af6f8-148">Windows Authentication</span></span> |

<span data-ttu-id="af6f8-149">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="af6f8-150">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="af6f8-150">Create a folder for the project.</span></span>
* <span data-ttu-id="af6f8-151">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="af6f8-151">Name the project.</span></span>

<span data-ttu-id="af6f8-152">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="af6f8-153">既存のアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="af6f8-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="af6f8-154"> サーバー アプリは、ASP.NET Core アプリと同じ方法でセキュリティ用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="af6f8-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="af6f8-155">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af6f8-155">For more information, see the articles under <xref:security/index>.</span></span>