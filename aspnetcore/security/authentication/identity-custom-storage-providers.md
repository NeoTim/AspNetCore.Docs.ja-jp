---
title: ASP.NET Core 用のカスタムストレージプロバイダーIdentity
author: ardalis
description: ASP.NET Core Identity用にカスタム記憶域プロバイダーを構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: c33267350f8f6b47f3ba649e96efd3d29fb116be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775233"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="0168d-103">ASP.NET Core 用のカスタムストレージプロバイダーIdentity</span><span class="sxs-lookup"><span data-stu-id="0168d-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="0168d-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0168d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0168d-105">ASP.NET Core Identityは拡張可能なシステムであり、カスタム記憶域プロバイダーを作成してアプリに接続することができます。</span><span class="sxs-lookup"><span data-stu-id="0168d-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="0168d-106">このトピックでは、ASP.NET Core Identity用にカスタマイズされた記憶域プロバイダーを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0168d-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="0168d-107">この記事では、独自の記憶域プロバイダーを作成するための重要な概念について説明しますが、詳細な手順については説明しません。</span><span class="sxs-lookup"><span data-stu-id="0168d-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="0168d-108">[GitHub のサンプルを表示またはダウンロードしてください](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)。</span><span class="sxs-lookup"><span data-stu-id="0168d-108">[View or download sample from GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="0168d-109">はじめに</span><span class="sxs-lookup"><span data-stu-id="0168d-109">Introduction</span></span>

<span data-ttu-id="0168d-110">既定では、ASP.NET Core Identityシステムは Entity Framework Core を使用して SQL Server データベースにユーザー情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="0168d-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="0168d-111">多くのアプリでは、この方法が適しています。</span><span class="sxs-lookup"><span data-stu-id="0168d-111">For many apps, this approach works well.</span></span> <span data-ttu-id="0168d-112">ただし、別の永続化メカニズムまたはデータスキーマを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0168d-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="0168d-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0168d-113">For example:</span></span>

* <span data-ttu-id="0168d-114">[Azure Table Storage](/azure/storage/)または別のデータストアを使用します。</span><span class="sxs-lookup"><span data-stu-id="0168d-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="0168d-115">データベーステーブルの構造が異なります。</span><span class="sxs-lookup"><span data-stu-id="0168d-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="0168d-116">[Dapper](https://github.com/StackExchange/Dapper)など、別のデータアクセス方法を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="0168d-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="0168d-117">これらの各ケースでは、ストレージメカニズム用にカスタマイズされたプロバイダーを作成し、そのプロバイダーをアプリに接続できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="0168d-118">ASP.NET Core Identityは、Visual Studio の [個々のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="0168d-119">.NET Core CLI を使用する場合は`-au Individual`、次のように追加します。</span><span class="sxs-lookup"><span data-stu-id="0168d-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="0168d-120">ASP.NET Core Identityアーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="0168d-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="0168d-121">ASP.NET Core Identityは、マネージャーとストアと呼ばれるクラスで構成されます。</span><span class="sxs-lookup"><span data-stu-id="0168d-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="0168d-122">*マネージャー*は、アプリ開発者がIdentityユーザーの作成などの操作を実行するために使用する高レベルのクラスです。</span><span class="sxs-lookup"><span data-stu-id="0168d-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="0168d-123">*ストア*は、ユーザーやロールなどのエンティティがどのように永続化されるかを指定する下位レベルのクラスです。</span><span class="sxs-lookup"><span data-stu-id="0168d-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="0168d-124">ストアはリポジトリパターンに従い、永続化メカニズムと密接に結び付いています。</span><span class="sxs-lookup"><span data-stu-id="0168d-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="0168d-125">マネージャーはストアから切り離されています。つまり、アプリケーションコードを変更することなく永続化メカニズムを置き換えることができます (構成を除く)。</span><span class="sxs-lookup"><span data-stu-id="0168d-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="0168d-126">次の図は、web アプリがマネージャーと対話する方法を示しています。また、ストアはデータアクセス層と対話します。</span><span class="sxs-lookup"><span data-stu-id="0168d-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core アプリは、マネージャー (たとえば、' UserManager ', ' RoleManager ') と連携します。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="0168d-129">カスタム記憶域プロバイダーを作成するには、データソース、データアクセス層、およびこのデータアクセス層と対話するストアクラスを作成します (上の図の緑と灰色のボックス)。</span><span class="sxs-lookup"><span data-stu-id="0168d-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="0168d-130">マネージャーや、それらと対話するアプリコード (上の青いボックス) をカスタマイズする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0168d-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="0168d-131">の`UserManager`新しいインスタンスを作成する場合`RoleManager` 、またはユーザークラスの型を指定し、ストアクラスのインスタンスを引数として渡します。</span><span class="sxs-lookup"><span data-stu-id="0168d-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="0168d-132">この方法を使用すると、カスタマイズしたクラスを ASP.NET Core に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="0168d-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="0168d-133">[新しいストレージプロバイダーを使用するようにアプリを再構成](#reconfigure-app-to-use-a-new-storage-provider)するカスタムストアでと`UserManager` `RoleManager`をインスタンス化する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0168d-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="0168d-134">データIdentity型を格納 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0168d-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="0168d-135">[ASP.NET Core Identity ](https://github.com/aspnet/identity)のデータ型については、次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="0168d-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="0168d-136">Users</span><span class="sxs-lookup"><span data-stu-id="0168d-136">Users</span></span>

<span data-ttu-id="0168d-137">Web サイトの登録済みユーザー。</span><span class="sxs-lookup"><span data-stu-id="0168d-137">Registered users of your web site.</span></span> <span data-ttu-id="0168d-138">[ユーザー](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)の種類は、独自のカスタム型の例として拡張または使用できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="0168d-139">独自のカスタム id ストレージソリューションを実装するために、特定の型から継承する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0168d-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="0168d-140">ユーザー要求</span><span class="sxs-lookup"><span data-stu-id="0168d-140">User Claims</span></span>

<span data-ttu-id="0168d-141">ユーザーの id を表す、ユーザーに関する一連のステートメント (または[要求](/dotnet/api/system.security.claims.claim))。</span><span class="sxs-lookup"><span data-stu-id="0168d-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="0168d-142">では、ロールを使用した場合よりも多くのユーザー id を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="0168d-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="0168d-143">ユーザーログイン</span><span class="sxs-lookup"><span data-stu-id="0168d-143">User Logins</span></span>

<span data-ttu-id="0168d-144">ユーザーのログイン時に使用する外部認証プロバイダー (Facebook や Microsoft アカウントなど) に関する情報。</span><span class="sxs-lookup"><span data-stu-id="0168d-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="0168d-145">例</span><span class="sxs-lookup"><span data-stu-id="0168d-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="0168d-146">ロール</span><span class="sxs-lookup"><span data-stu-id="0168d-146">Roles</span></span>

<span data-ttu-id="0168d-147">サイトの承認グループ。</span><span class="sxs-lookup"><span data-stu-id="0168d-147">Authorization groups for your site.</span></span> <span data-ttu-id="0168d-148">ロール Id とロール名 ("Admin" や "Employee" など) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0168d-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="0168d-149">例</span><span class="sxs-lookup"><span data-stu-id="0168d-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="0168d-150">データアクセス層</span><span class="sxs-lookup"><span data-stu-id="0168d-150">The data access layer</span></span>

<span data-ttu-id="0168d-151">このトピックでは、使用する永続化メカニズムについて理解していること、およびそのメカニズムのエンティティを作成する方法について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="0168d-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="0168d-152">このトピックでは、リポジトリまたはデータアクセスクラスの作成方法の詳細については説明しません。ASP.NET Core Identityを使用する場合の設計上の決定について、いくつかの提案を提供します。</span><span class="sxs-lookup"><span data-stu-id="0168d-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="0168d-153">カスタマイズされたストアプロバイダーのデータアクセス層を設計する際には、自由度が高くなります。</span><span class="sxs-lookup"><span data-stu-id="0168d-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="0168d-154">アプリで使用する予定の機能に対してのみ、永続化メカニズムを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168d-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="0168d-155">たとえば、アプリでロールを使用していない場合は、ロールまたはユーザーロールの関連付け用のストレージを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0168d-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="0168d-156">テクノロジと既存のインフラストラクチャでは、ASP.NET Core Identityの既定の実装とは大きく異なる構造が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0168d-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="0168d-157">データアクセス層では、ストレージ実装の構造を操作するロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="0168d-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="0168d-158">データアクセス層は、ASP.NET Core Identityからデータソースにデータを保存するためのロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="0168d-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="0168d-159">カスタマイズした記憶域プロバイダーのデータアクセス層には、ユーザーとロールの情報を格納するための次のクラスが含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="0168d-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="0168d-160">Context クラス</span><span class="sxs-lookup"><span data-stu-id="0168d-160">Context class</span></span>

<span data-ttu-id="0168d-161">永続化メカニズムに接続してクエリを実行するための情報をカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="0168d-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="0168d-162">いくつかのデータクラスには、通常、依存関係の挿入によって提供されるこのクラスのインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="0168d-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="0168d-163">[例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="0168d-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="0168d-164">ユーザーストレージ</span><span class="sxs-lookup"><span data-stu-id="0168d-164">User Storage</span></span>

<span data-ttu-id="0168d-165">ユーザー情報 (ユーザー名やパスワードハッシュなど) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="0168d-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="0168d-166">例</span><span class="sxs-lookup"><span data-stu-id="0168d-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="0168d-167">ロールストレージ</span><span class="sxs-lookup"><span data-stu-id="0168d-167">Role Storage</span></span>

<span data-ttu-id="0168d-168">ロール名などのロール情報を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="0168d-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="0168d-169">例</span><span class="sxs-lookup"><span data-stu-id="0168d-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="0168d-170">UserClaims ストレージ</span><span class="sxs-lookup"><span data-stu-id="0168d-170">UserClaims Storage</span></span>

<span data-ttu-id="0168d-171">ユーザー要求情報 (要求の種類や値など) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="0168d-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="0168d-172">例</span><span class="sxs-lookup"><span data-stu-id="0168d-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="0168d-173">UserLogins ストレージ</span><span class="sxs-lookup"><span data-stu-id="0168d-173">UserLogins Storage</span></span>

<span data-ttu-id="0168d-174">ユーザーのログイン情報 (外部認証プロバイダーなど) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="0168d-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="0168d-175">例</span><span class="sxs-lookup"><span data-stu-id="0168d-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="0168d-176">UserRole ストレージ</span><span class="sxs-lookup"><span data-stu-id="0168d-176">UserRole Storage</span></span>

<span data-ttu-id="0168d-177">どのロールがどのユーザーに割り当てられているかを格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="0168d-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="0168d-178">例</span><span class="sxs-lookup"><span data-stu-id="0168d-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="0168d-179">**ヒント:** アプリで使用する予定のクラスのみを実装します。</span><span class="sxs-lookup"><span data-stu-id="0168d-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="0168d-180">データアクセスクラスで、永続化メカニズムのデータ操作を実行するコードを指定します。</span><span class="sxs-lookup"><span data-stu-id="0168d-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="0168d-181">たとえば、カスタムプロバイダー内で、*ストア*クラスに新しいユーザーを作成するには、次のコードが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="0168d-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="0168d-182">ユーザーを作成するための実装ロジックは、 `_usersTable.CreateAsync`次に示すメソッドに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="0168d-183">ユーザークラスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168d-183">Customize the user class</span></span>

<span data-ttu-id="0168d-184">ストレージプロバイダーを実装する場合は、ユーザークラスを作成します。このクラスは、[ユーザークラスに](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)相当します。</span><span class="sxs-lookup"><span data-stu-id="0168d-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="0168d-185">少なくとも、ユーザークラスには`Id` `UserName`プロパティとプロパティが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168d-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="0168d-186">クラス`IdentityUser`は、要求された`UserManager`操作の実行時にが呼び出すプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="0168d-187">`Id`プロパティの既定の型は文字列ですが、から`IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`継承して別の型を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="0168d-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="0168d-188">フレームワークでは、ストレージの実装がデータ型の変換を処理することを想定しています。</span><span class="sxs-lookup"><span data-stu-id="0168d-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="0168d-189">ユーザーストアをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168d-189">Customize the user store</span></span>

<span data-ttu-id="0168d-190">ユーザーの`UserStore`すべてのデータ操作のメソッドを提供するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0168d-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="0168d-191">このクラスは、 [userstore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)クラスに相当します。</span><span class="sxs-lookup"><span data-stu-id="0168d-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="0168d-192">`UserStore`クラスで、および必要`IUserStore<TUser>`に応じて省略可能なインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0168d-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="0168d-193">実装するオプションのインターフェイスは、アプリで提供される機能に基づいて選択します。</span><span class="sxs-lookup"><span data-stu-id="0168d-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="0168d-194">省略可能なインターフェイス</span><span class="sxs-lookup"><span data-stu-id="0168d-194">Optional interfaces</span></span>

* [<span data-ttu-id="0168d-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="0168d-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="0168d-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="0168d-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="0168d-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="0168d-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="0168d-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="0168d-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="0168d-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="0168d-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="0168d-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="0168d-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="0168d-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="0168d-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="0168d-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="0168d-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="0168d-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="0168d-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="0168d-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="0168d-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="0168d-205">オプションのインターフェイスは、 `IUserStore<TUser>`から継承されます。</span><span class="sxs-lookup"><span data-stu-id="0168d-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="0168d-206">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)には、部分的に実装されたサンプルユーザーストアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0168d-206">You can see a partially implemented sample user store in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="0168d-207">`UserStore`クラス内では、操作を実行するために作成したデータアクセスクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="0168d-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="0168d-208">これらは、依存関係の挿入を使用して渡されます。</span><span class="sxs-lookup"><span data-stu-id="0168d-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="0168d-209">たとえば、Dapper 実装の SQL Server では、 `UserStore`クラスには、の`CreateAsync` `DapperUsersTable`インスタンスを使用して新しいレコードを挿入するメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="0168d-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="0168d-210">ユーザーストアをカスタマイズするときに実装するインターフェイス</span><span class="sxs-lookup"><span data-stu-id="0168d-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="0168d-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-211">**IUserStore**</span></span>  
 <span data-ttu-id="0168d-212">[&lt;IUserStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)インターフェイスは、ユーザーストアに実装する必要がある唯一のインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="0168d-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="0168d-213">ユーザーの作成、更新、削除、および取得を行うためのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="0168d-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="0168d-215">[&lt;IUserClaimStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)インターフェイスでは、ユーザーの信頼性情報を有効にするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="0168d-216">これには、ユーザー要求を追加、削除、および取得するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="0168d-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="0168d-218">[&lt;IUserLoginStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)は、外部認証プロバイダーを有効にするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="0168d-219">これには、ユーザーログインを追加、削除、取得するためのメソッド、およびログイン情報に基づいてユーザーを取得するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="0168d-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="0168d-221">[&lt;IUserRoleStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)インターフェイスでは、ユーザーをロールにマップするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="0168d-222">これには、ユーザーのロールを追加、削除、取得するメソッド、およびユーザーがロールに割り当てられているかどうかを確認するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="0168d-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="0168d-224">[&lt;IUserPasswordStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)インターフェイスは、ハッシュされたパスワードを保持するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="0168d-225">ハッシュされたパスワードを取得および設定するためのメソッドと、ユーザーがパスワードを設定したかどうかを示すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="0168d-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="0168d-227">[&lt;IUserSecurityStampStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)インターフェイスでは、ユーザーのアカウント情報が変更されたかどうかを示すセキュリティスタンプを使用するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="0168d-228">このスタンプは、ユーザーがパスワードを変更したとき、またはログインを追加または削除したときに更新されます。</span><span class="sxs-lookup"><span data-stu-id="0168d-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="0168d-229">セキュリティスタンプを取得および設定するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="0168d-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="0168d-231">[&lt;IUserTwoFactorStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)インターフェイスでは、2要素認証をサポートするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="0168d-232">これには、ユーザーに対して2要素認証を有効にするかどうかを取得および設定するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="0168d-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="0168d-234">[&lt;IUserPhoneNumberStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)インターフェイスでは、ユーザーの電話番号を格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="0168d-235">電話番号を取得して設定するためのメソッド、および電話番号を確認する方法が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="0168d-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="0168d-237">[&lt;IUserEmailStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)インターフェイスでは、ユーザーの電子メールアドレスを格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="0168d-238">このファイルには、電子メールアドレスを取得および設定するためのメソッドと、電子メールを確認するかどうかが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="0168d-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="0168d-240">[&lt;IUserLockoutStore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)インターフェイスは、アカウントのロックに関する情報を格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="0168d-241">失敗したアクセス試行とロックアウトを追跡するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="0168d-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="0168d-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="0168d-243">[&lt;Iqueryableuserstore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)インターフェイスは、クエリ可能なユーザーストアを提供するために実装するメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="0168d-244">アプリケーションで必要なインターフェイスだけを実装します。</span><span class="sxs-lookup"><span data-stu-id="0168d-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="0168d-245">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0168d-245">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="0168d-246">ユーザー Id、ユーザー名、およびユーザー名</span><span class="sxs-lookup"><span data-stu-id="0168d-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="0168d-247">名前`Microsoft.AspNet.Identity.EntityFramework`空間[には、](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)ユーザー [id](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)クラスの実装が含ま[れてい](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)ます。</span><span class="sxs-lookup"><span data-stu-id="0168d-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="0168d-248">これらの機能を使用している場合は、これらのクラスの独自のバージョンを作成し、アプリのプロパティを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="0168d-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="0168d-249">ただし、基本操作 (ユーザーの要求の追加や削除など) を実行するときに、これらのエンティティをメモリに読み込まない方が効率的な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="0168d-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="0168d-250">代わりに、バックエンドストアクラスは、データソースでこれらの操作を直接実行できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="0168d-251">たとえば、 `UserStore.GetClaimsAsync`メソッドは、 `userClaimTable.FindByUserId(user.Id)`メソッドを呼び出して、そのテーブルに対してクエリを直接実行し、クレームの一覧を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="0168d-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="0168d-252">ロールクラスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168d-252">Customize the role class</span></span>

<span data-ttu-id="0168d-253">ロールストレージプロバイダーを実装する場合は、カスタムロールの種類を作成できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="0168d-254">特定のインターフェイスを実装する必要はありませんが、 `Id`が必要であり、通常`Name`はプロパティを持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168d-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="0168d-255">ロールクラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0168d-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="0168d-256">ロールストアをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168d-256">Customize the role store</span></span>

<span data-ttu-id="0168d-257">ロールに対するすべて`RoleStore`のデータ操作のメソッドを提供するクラスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="0168d-258">このクラスは、 [rolestore&lt;trole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)クラスに相当します。</span><span class="sxs-lookup"><span data-stu-id="0168d-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="0168d-259">`RoleStore`クラスでは、 `IRoleStore<TRole>`および必要に応じて`IQueryableRoleStore<TRole>`インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0168d-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="0168d-260">**IRoleStore&lt;trole&gt;**</span><span class="sxs-lookup"><span data-stu-id="0168d-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="0168d-261">[Irolestore&lt;&gt; trole](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)インターフェイスは、ロールストアクラスに実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="0168d-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="0168d-262">これには、ロールの作成、更新、削除、および取得を行うためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="0168d-263">**RoleStore&lt;trole&gt;**</span><span class="sxs-lookup"><span data-stu-id="0168d-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="0168d-264">カスタマイズ`RoleStore`するには、 `IRoleStore<TRole>`インターフェイスを実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0168d-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="0168d-265">新しい記憶域プロバイダーを使用するようにアプリを再構成する</span><span class="sxs-lookup"><span data-stu-id="0168d-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="0168d-266">ストレージプロバイダーを実装したら、それを使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="0168d-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="0168d-267">アプリで既定のプロバイダーが使用されている場合は、カスタムプロバイダーに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0168d-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="0168d-268">NuGet パッケージ`Microsoft.AspNetCore.EntityFramework.Identity`を削除します。</span><span class="sxs-lookup"><span data-stu-id="0168d-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="0168d-269">ストレージプロバイダーが別のプロジェクトまたはパッケージに存在する場合は、その記憶域プロバイダーへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="0168d-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="0168d-270">へのすべての`Microsoft.AspNetCore.EntityFramework.Identity`参照を、ストレージプロバイダーの名前空間の using ステートメントに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0168d-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="0168d-271">`ConfigureServices`メソッドで、カスタム型を`AddIdentity`使用するようにメソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="0168d-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="0168d-272">この目的のために独自の拡張メソッドを作成できます。</span><span class="sxs-lookup"><span data-stu-id="0168d-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="0168d-273">例については、「 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0168d-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="0168d-274">ロールを使用している場合は`RoleManager` 、 `RoleStore`クラスを使用するようにを更新します。</span><span class="sxs-lookup"><span data-stu-id="0168d-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="0168d-275">接続文字列と資格情報をアプリの構成に更新します。</span><span class="sxs-lookup"><span data-stu-id="0168d-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="0168d-276">例:</span><span class="sxs-lookup"><span data-stu-id="0168d-276">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="0168d-277">References</span><span class="sxs-lookup"><span data-stu-id="0168d-277">References</span></span>

* <span data-ttu-id="0168d-278">[ASP.NET 4.x 用のカスタムストレージプロバイダーIdentity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span><span class="sxs-lookup"><span data-stu-id="0168d-278">[Custom Storage Providers for ASP.NET 4.x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span></span>
* <span data-ttu-id="0168d-279">[ASP.NET Core Identity ](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash;このリポジトリには、コミュニティによって管理されるストアプロバイダーへのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168d-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; This repository includes links to community maintained store providers.</span></span>
