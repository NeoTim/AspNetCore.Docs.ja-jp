---
title: 承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する
author: rick-anderson
description: Razor承認によって保護されたユーザーデータを含むページアプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、ASP.NET Core が含まれ Identity ます。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f50015af864a4a62abd5e2eab508aac915cb6370
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404718"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="83f0e-104">承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="83f0e-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="83f0e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="83f0e-106">ASP.NET Core MVC バージョンについては、[この PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83f0e-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="83f0e-107">このチュートリアルの ASP.NET Core 1.1 バージョンは、[この](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="83f0e-108">1.1 ASP.NET Core サンプルは、「」[のサンプルに](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="83f0e-109">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="83f0e-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83f0e-110">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="83f0e-111">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="83f0e-112">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-112">There are three security groups:</span></span>

* <span data-ttu-id="83f0e-113">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="83f0e-114">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="83f0e-115">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="83f0e-116">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="83f0e-117">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="83f0e-118">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="83f0e-119">Rick は、承認された連絡先を表示したり、[削除] を**編集**したりするだけで、 / **Delete** / 連絡先の新しいリンクを**作成**できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="83f0e-120">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="83f0e-121">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="83f0e-123">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="83f0e-125">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-125">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="83f0e-127">[**承認**] ボタンと [**却下**] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="83f0e-128">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="83f0e-130">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-130">The administrator has all privileges.</span></span> <span data-ttu-id="83f0e-131">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="83f0e-132">アプリは、次のモデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="83f0e-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="83f0e-133">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="83f0e-134">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="83f0e-135">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="83f0e-136">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="83f0e-137">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83f0e-137">Prerequisites</span></span>

<span data-ttu-id="83f0e-138">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="83f0e-138">This tutorial is advanced.</span></span> <span data-ttu-id="83f0e-139">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-139">You should be familiar with:</span></span>

* [<span data-ttu-id="83f0e-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f0e-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="83f0e-141">認証</span><span class="sxs-lookup"><span data-stu-id="83f0e-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="83f0e-142">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="83f0e-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="83f0e-143">承認</span><span class="sxs-lookup"><span data-stu-id="83f0e-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="83f0e-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="83f0e-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="83f0e-145">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="83f0e-145">The starter and completed app</span></span>

<span data-ttu-id="83f0e-146">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="83f0e-147">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="83f0e-148">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="83f0e-148">The starter app</span></span>

<span data-ttu-id="83f0e-149">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="83f0e-150">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="83f0e-151">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="83f0e-151">Secure user data</span></span>

<span data-ttu-id="83f0e-152">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="83f0e-153">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="83f0e-154">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="83f0e-154">Tie the contact data to the user</span></span>

<span data-ttu-id="83f0e-155">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="83f0e-156">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="83f0e-157">`OwnerID`データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="83f0e-158">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="83f0e-159">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="83f0e-160">役割サービスの追加先Identity</span><span class="sxs-lookup"><span data-stu-id="83f0e-160">Add Role services to Identity</span></span>

<span data-ttu-id="83f0e-161">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="83f0e-162">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="83f0e-162">Require authenticated users</span></span>

<span data-ttu-id="83f0e-163">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="83f0e-164">Razorページ、コントローラー、またはアクションメソッドレベルで、属性を使用して認証を無効にすることができ `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="83f0e-165">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加されたページとコントローラーは保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="83f0e-166">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="83f0e-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="83f0e-167">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)をインデックスおよびプライバシーページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="83f0e-168">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-168">Configure the test account</span></span>

<span data-ttu-id="83f0e-169">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="83f0e-170">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="83f0e-171">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="83f0e-172">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="83f0e-173">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="83f0e-174">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="83f0e-175">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="83f0e-176">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="83f0e-177">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="83f0e-178">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="83f0e-179">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="83f0e-180">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="83f0e-181">`ContactIsOwnerAuthorizationHandler`*承認*フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="83f0e-182">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="83f0e-183">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し[ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="83f0e-184">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="83f0e-185">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="83f0e-186">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="83f0e-187">`Task.CompletedTask`が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="83f0e-188">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="83f0e-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="83f0e-189">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="83f0e-190">`ContactIsOwnerAuthorizationHandler`要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="83f0e-191">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-191">Create a manager authorization handler</span></span>

<span data-ttu-id="83f0e-192">`ContactManagerAuthorizationHandler`*承認*フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="83f0e-193">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="83f0e-194">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="83f0e-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="83f0e-195">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="83f0e-196">`ContactAdministratorsAuthorizationHandler`*承認*フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="83f0e-197">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="83f0e-198">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="83f0e-199">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="83f0e-199">Register the authorization handlers</span></span>

<span data-ttu-id="83f0e-200">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="83f0e-201">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="83f0e-202">サービスコレクションにハンドラーを登録し `ContactsController` て、[依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="83f0e-203">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="83f0e-204">`ContactAdministratorsAuthorizationHandler`と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="83f0e-205">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="83f0e-206">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="83f0e-206">Support authorization</span></span>

<span data-ttu-id="83f0e-207">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="83f0e-208">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="83f0e-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="83f0e-209">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="83f0e-210">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="83f0e-211">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="83f0e-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="83f0e-212">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="83f0e-213">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="83f0e-214">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-214">The preceding code:</span></span>

* <span data-ttu-id="83f0e-215">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="83f0e-216">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="83f0e-217">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="83f0e-218">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-218">Update the CreateModel</span></span>

<span data-ttu-id="83f0e-219">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="83f0e-220">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="83f0e-221">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="83f0e-222">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="83f0e-223">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-223">Update the IndexModel</span></span>

<span data-ttu-id="83f0e-224">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="83f0e-225">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-225">Update the EditModel</span></span>

<span data-ttu-id="83f0e-226">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="83f0e-227">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="83f0e-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="83f0e-228">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="83f0e-229">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="83f0e-230">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="83f0e-231">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="83f0e-232">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-232">Update the DeleteModel</span></span>

<span data-ttu-id="83f0e-233">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="83f0e-234">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="83f0e-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="83f0e-235">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="83f0e-236">*ページ/_ViewImports*ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="83f0e-237">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="83f0e-238">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="83f0e-239">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="83f0e-240">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="83f0e-241">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="83f0e-242">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="83f0e-243">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="83f0e-243">Update Details</span></span>

<span data-ttu-id="83f0e-244">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="83f0e-245">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="83f0e-246">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="83f0e-246">Add or remove a user to a role</span></span>

<span data-ttu-id="83f0e-247">次の情報については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83f0e-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="83f0e-248">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-248">Removing privileges from a user.</span></span> <span data-ttu-id="83f0e-249">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="83f0e-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="83f0e-250">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="83f0e-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="83f0e-251">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="83f0e-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="83f0e-252">このアプリでは、認証された[ユーザーを要求](#require-authenticated-users)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="83f0e-253">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-253">The following code allows anonymous users.</span></span> <span data-ttu-id="83f0e-254">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="83f0e-255">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-255">In the preceding code:</span></span>

* <span data-ttu-id="83f0e-256">ユーザーが認証**されていない**場合は、 `ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="83f0e-257">が返されると、 `ChallengeResult` ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="83f0e-258">ユーザーが認証されているが承認されていない場合は、 `ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="83f0e-259">が返されると、ユーザーは [ `ForbidResult` アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="83f0e-260">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="83f0e-260">Test the completed app</span></span>

<span data-ttu-id="83f0e-261">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="83f0e-262">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="83f0e-263">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="83f0e-264">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="83f0e-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="83f0e-265">アプリに連絡先がある場合:</span><span class="sxs-lookup"><span data-stu-id="83f0e-265">If the app has contacts:</span></span>

* <span data-ttu-id="83f0e-266">テーブル内のすべてのレコードを削除 `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="83f0e-267">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="83f0e-268">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="83f0e-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="83f0e-269">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="83f0e-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="83f0e-270">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="83f0e-271">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-271">Verify the following operations:</span></span>

* <span data-ttu-id="83f0e-272">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="83f0e-273">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="83f0e-274">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="83f0e-275">このビューには、 `Details` [**承認**] ボタンと [**却下**] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="83f0e-276">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="83f0e-277">ユーザー</span><span class="sxs-lookup"><span data-stu-id="83f0e-277">User</span></span>                | <span data-ttu-id="83f0e-278">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="83f0e-278">Seeded by the app</span></span> | <span data-ttu-id="83f0e-279">オプション</span><span class="sxs-lookup"><span data-stu-id="83f0e-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="83f0e-280">いいえ</span><span class="sxs-lookup"><span data-stu-id="83f0e-280">No</span></span>                | <span data-ttu-id="83f0e-281">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="83f0e-282">はい</span><span class="sxs-lookup"><span data-stu-id="83f0e-282">Yes</span></span>               | <span data-ttu-id="83f0e-283">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="83f0e-284">はい</span><span class="sxs-lookup"><span data-stu-id="83f0e-284">Yes</span></span>               | <span data-ttu-id="83f0e-285">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="83f0e-286">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="83f0e-287">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="83f0e-288">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="83f0e-289">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-289">Create the starter app</span></span>

* <span data-ttu-id="83f0e-290">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="83f0e-291">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="83f0e-292">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="83f0e-293">`-uld`SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="83f0e-294">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="83f0e-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="83f0e-295">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="83f0e-296">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="83f0e-297">コマンドでバグが発生した場合は `dotnet aspnet-codegenerator razorpage` 、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83f0e-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="83f0e-298">*Pages/Shared/_Layout cshtml*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="83f0e-299">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="83f0e-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="83f0e-300">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="83f0e-300">Seed the database</span></span>

<span data-ttu-id="83f0e-301">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="83f0e-302">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="83f0e-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="83f0e-303">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-303">Test that the app seeded the database.</span></span> <span data-ttu-id="83f0e-304">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="83f0e-305">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="83f0e-306">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="83f0e-307">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-307">There are three security groups:</span></span>

* <span data-ttu-id="83f0e-308">**登録されているユーザー**は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="83f0e-309">**管理者**は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="83f0e-310">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="83f0e-311">**管理者**は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="83f0e-312">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="83f0e-313">Rick は、承認された連絡先を表示したり、[削除] を**編集**したりするだけで、 / **Delete** / 連絡先の新しいリンクを**作成**できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="83f0e-314">Rick によって作成された最後のレコードにのみ、**編集**および**削除**のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="83f0e-315">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="83f0e-317">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="83f0e-319">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-319">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="83f0e-321">[**承認**] ボタンと [**却下**] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="83f0e-322">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="83f0e-324">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-324">The administrator has all privileges.</span></span> <span data-ttu-id="83f0e-325">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="83f0e-326">アプリは、次のモデルを[スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="83f0e-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="83f0e-327">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="83f0e-328">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="83f0e-329">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="83f0e-330">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="83f0e-331">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83f0e-331">Prerequisites</span></span>

<span data-ttu-id="83f0e-332">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="83f0e-332">This tutorial is advanced.</span></span> <span data-ttu-id="83f0e-333">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-333">You should be familiar with:</span></span>

* [<span data-ttu-id="83f0e-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f0e-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="83f0e-335">認証</span><span class="sxs-lookup"><span data-stu-id="83f0e-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="83f0e-336">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="83f0e-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="83f0e-337">承認</span><span class="sxs-lookup"><span data-stu-id="83f0e-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="83f0e-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="83f0e-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="83f0e-339">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="83f0e-339">The starter and completed app</span></span>

<span data-ttu-id="83f0e-340">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="83f0e-341">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="83f0e-342">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="83f0e-342">The starter app</span></span>

<span data-ttu-id="83f0e-343">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="83f0e-344">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="83f0e-345">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="83f0e-345">Secure user data</span></span>

<span data-ttu-id="83f0e-346">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="83f0e-347">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="83f0e-348">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="83f0e-348">Tie the contact data to the user</span></span>

<span data-ttu-id="83f0e-349">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="83f0e-350">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="83f0e-351">`OwnerID`データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="83f0e-352">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="83f0e-353">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="83f0e-354">役割サービスの追加先Identity</span><span class="sxs-lookup"><span data-stu-id="83f0e-354">Add Role services to Identity</span></span>

<span data-ttu-id="83f0e-355">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="83f0e-356">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="83f0e-356">Require authenticated users</span></span>

<span data-ttu-id="83f0e-357">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="83f0e-358">Razorページ、コントローラー、またはアクションメソッドレベルで、属性を使用して認証を無効にすることができ `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="83f0e-359">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加されたページとコントローラーは保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="83f0e-360">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="83f0e-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="83f0e-361">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="83f0e-362">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-362">Configure the test account</span></span>

<span data-ttu-id="83f0e-363">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="83f0e-364">これらのアカウントのパスワードを設定するには、[シークレットマネージャーツール](xref:security/app-secrets)を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="83f0e-365">プロジェクトディレクトリ ( *Program.cs*を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="83f0e-366">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="83f0e-367">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="83f0e-368">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="83f0e-369">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="83f0e-370">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="83f0e-371">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="83f0e-372">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="83f0e-373">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="83f0e-374">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="83f0e-375">*承認*フォルダーを作成し、 `ContactIsOwnerAuthorizationHandler` そこにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="83f0e-376">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="83f0e-377">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し[ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="83f0e-378">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="83f0e-379">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="83f0e-380">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="83f0e-381">`Task.CompletedTask`が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="83f0e-382">明示的に失敗する必要がある場合は、コンテキストを返し[ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="83f0e-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="83f0e-383">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="83f0e-384">`ContactIsOwnerAuthorizationHandler`要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="83f0e-385">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-385">Create a manager authorization handler</span></span>

<span data-ttu-id="83f0e-386">`ContactManagerAuthorizationHandler`*承認*フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="83f0e-387">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="83f0e-388">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="83f0e-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="83f0e-389">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="83f0e-390">`ContactAdministratorsAuthorizationHandler`*承認*フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="83f0e-391">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="83f0e-392">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="83f0e-393">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="83f0e-393">Register the authorization handlers</span></span>

<span data-ttu-id="83f0e-394">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="83f0e-395">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="83f0e-396">サービスコレクションにハンドラーを登録し `ContactsController` て、[依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="83f0e-397">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="83f0e-398">`ContactAdministratorsAuthorizationHandler`と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="83f0e-399">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="83f0e-400">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="83f0e-400">Support authorization</span></span>

<span data-ttu-id="83f0e-401">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="83f0e-402">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="83f0e-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="83f0e-403">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="83f0e-404">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="83f0e-405">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="83f0e-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="83f0e-406">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="83f0e-407">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="83f0e-408">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-408">The preceding code:</span></span>

* <span data-ttu-id="83f0e-409">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="83f0e-410">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="83f0e-411">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="83f0e-412">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-412">Update the CreateModel</span></span>

<span data-ttu-id="83f0e-413">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="83f0e-414">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="83f0e-415">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="83f0e-416">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="83f0e-417">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-417">Update the IndexModel</span></span>

<span data-ttu-id="83f0e-418">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="83f0e-419">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-419">Update the EditModel</span></span>

<span data-ttu-id="83f0e-420">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="83f0e-421">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="83f0e-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="83f0e-422">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="83f0e-423">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="83f0e-424">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="83f0e-425">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="83f0e-426">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-426">Update the DeleteModel</span></span>

<span data-ttu-id="83f0e-427">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="83f0e-428">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="83f0e-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="83f0e-429">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="83f0e-430">すべてのビューで使用できるように、 *views/_ViewImports cshtml*ファイルに承認サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="83f0e-431">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="83f0e-432">*Pages/Contacts/Index. cshtml*の**編集**と**削除**のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="83f0e-433">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="83f0e-434">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="83f0e-435">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="83f0e-436">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83f0e-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="83f0e-437">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="83f0e-437">Update Details</span></span>

<span data-ttu-id="83f0e-438">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="83f0e-439">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="83f0e-440">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="83f0e-440">Add or remove a user to a role</span></span>

<span data-ttu-id="83f0e-441">次の情報については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83f0e-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="83f0e-442">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-442">Removing privileges from a user.</span></span> <span data-ttu-id="83f0e-443">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="83f0e-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="83f0e-444">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="83f0e-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="83f0e-445">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="83f0e-445">Test the completed app</span></span>

<span data-ttu-id="83f0e-446">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、[シークレットマネージャーツール](xref:security/app-secrets#secret-manager)を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="83f0e-447">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="83f0e-448">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="83f0e-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="83f0e-449">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="83f0e-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="83f0e-450">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="83f0e-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="83f0e-451">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="83f0e-452">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="83f0e-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="83f0e-453">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="83f0e-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="83f0e-454">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="83f0e-455">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-455">Verify the following operations:</span></span>

* <span data-ttu-id="83f0e-456">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="83f0e-457">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="83f0e-458">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="83f0e-459">このビューには、 `Details` [**承認**] ボタンと [**却下**] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="83f0e-460">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="83f0e-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="83f0e-461">ユーザー</span><span class="sxs-lookup"><span data-stu-id="83f0e-461">User</span></span>                | <span data-ttu-id="83f0e-462">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="83f0e-462">Seeded by the app</span></span> | <span data-ttu-id="83f0e-463">オプション</span><span class="sxs-lookup"><span data-stu-id="83f0e-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="83f0e-464">いいえ</span><span class="sxs-lookup"><span data-stu-id="83f0e-464">No</span></span>                | <span data-ttu-id="83f0e-465">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="83f0e-466">はい</span><span class="sxs-lookup"><span data-stu-id="83f0e-466">Yes</span></span>               | <span data-ttu-id="83f0e-467">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="83f0e-468">はい</span><span class="sxs-lookup"><span data-stu-id="83f0e-468">Yes</span></span>               | <span data-ttu-id="83f0e-469">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="83f0e-470">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="83f0e-471">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="83f0e-472">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="83f0e-473">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-473">Create the starter app</span></span>

* <span data-ttu-id="83f0e-474">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="83f0e-475">**個々のユーザーアカウント**を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="83f0e-476">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="83f0e-477">`-uld`SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="83f0e-478">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="83f0e-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="83f0e-479">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="83f0e-480">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="83f0e-481">*Pages/_Layout cshtml*ファイルで**contactmanager**アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="83f0e-482">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="83f0e-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="83f0e-483">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="83f0e-483">Seed the database</span></span>

<span data-ttu-id="83f0e-484">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを*Data*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="83f0e-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="83f0e-485">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="83f0e-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="83f0e-486">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="83f0e-486">Test that the app seeded the database.</span></span> <span data-ttu-id="83f0e-487">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="83f0e-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="83f0e-488">その他の資料</span><span class="sxs-lookup"><span data-stu-id="83f0e-488">Additional resources</span></span>

* [<span data-ttu-id="83f0e-489">Azure App Service で .NET Core および SQL Database のアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="83f0e-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="83f0e-490">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="83f0e-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="83f0e-491">このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83f0e-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="83f0e-492">カスタムポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="83f0e-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
