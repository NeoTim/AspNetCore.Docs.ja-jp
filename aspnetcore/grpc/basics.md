---
title: C# を使用した gRPC サービス
author: juntaoluo
description: で gRPC サービスをC#作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925183"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="e0ba9-103">gRPC サービスと C\#</span><span class="sxs-lookup"><span data-stu-id="e0ba9-103">gRPC services with C\#</span></span>

<span data-ttu-id="e0ba9-104">このドキュメントでは、でC# [grpc](https://grpc.io/docs/guides/)アプリを作成するために必要な概念の概要を説明します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="e0ba9-105">ここで説明するトピックは、 [C コア](https://grpc.io/blog/grpc-stacks)ベースと ASP.NET Core ベースの grpc アプリの両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="e0ba9-106">プロトコルファイル</span><span class="sxs-lookup"><span data-stu-id="e0ba9-106">proto file</span></span>

<span data-ttu-id="e0ba9-107">gRPC では、API 開発に対してコントラクト優先のアプローチが使われます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="e0ba9-108">プロトコルバッファー (protobuf) は、既定ではインターフェイスデザイン言語 (IDL) として使用されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="e0ba9-109">*@No__t の-1*ファイルには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="e0ba9-110">GRPC サービスの定義。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="e0ba9-111">クライアントとサーバーの間で送信されるメッセージ。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="e0ba9-112">Protobuf ファイルの構文の詳細については、[公式のドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="e0ba9-113">たとえば、「 [gRPC サービスの](xref:tutorials/grpc/grpc-start)使用」で使用されている*あいさつ*ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="e0ba9-114">サービスを`Greeter`定義します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="e0ba9-115">サービス`Greeter`は呼び出しを`SayHello`定義します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="e0ba9-116">`SayHello`メッセージを送信し、メッセージ`HelloReply`を受信します。 `HelloRequest`</span><span class="sxs-lookup"><span data-stu-id="e0ba9-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="e0ba9-117">プロトコルファイルを C\#アプリに追加する</span><span class="sxs-lookup"><span data-stu-id="e0ba9-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="e0ba9-118">*@No__t-1*ファイルは、次のように @no__t 項目グループに追加することによってプロジェクトに含められます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="e0ba9-119">.proto ファイルに対する C# ツール サポート</span><span class="sxs-lookup"><span data-stu-id="e0ba9-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="e0ba9-120">ファイルからC# *アセットを生成するには、ツールパッケージ [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) が必要\*です。*</span><span class="sxs-lookup"><span data-stu-id="e0ba9-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="e0ba9-121">生成されたアセット (ファイル):</span><span class="sxs-lookup"><span data-stu-id="e0ba9-121">The generated assets (files):</span></span>

* <span data-ttu-id="e0ba9-122">は、プロジェクトがビルドされるたびに、必要に応じて生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="e0ba9-123">プロジェクトに追加されていないか、ソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="e0ba9-124">は、 *obj*ディレクトリに含まれるビルド成果物です。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="e0ba9-125">このパッケージは、サーバープロジェクトとクライアントプロジェクトの両方で必要です。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="e0ba9-126">メタパッケージ`Grpc.AspNetCore`には、へ`Grpc.Tools`の参照が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="e0ba9-127">サーバープロジェクトは、 `Grpc.AspNetCore` Visual Studio のパッケージマネージャーを使用するか、プロジェクト`<PackageReference>`ファイルにを追加することによって追加できます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="e0ba9-128">クライアントプロジェクトは、grpc クライアントを使用するために必要な他のパッケージと一緒に直接参照`Grpc.Tools`する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="e0ba9-129">ツールパッケージは実行時に必須ではないため、依存関係`PrivateAssets="All"`は次のようにマークされます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="e0ba9-130">生成C#されたアセット</span><span class="sxs-lookup"><span data-stu-id="e0ba9-130">Generated C# assets</span></span>

<span data-ttu-id="e0ba9-131">ツールパッケージは、含まC#れている *\** ファイルで定義されているメッセージを表す型を生成します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="e0ba9-132">サーバー側アセットの場合、抽象サービスの基本型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="e0ba9-133">基本型には、 *proto*ファイルに含まれるすべての grpc 呼び出しの定義が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="e0ba9-134">この基本型から派生し、gRPC 呼び出しのロジックを実装する具象サービス実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="e0ba9-135">では、前に説明した例で`GreeterBase`は、仮想`SayHello`メソッドを含む抽象型が生成されます。 `greet.proto`</span><span class="sxs-lookup"><span data-stu-id="e0ba9-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="e0ba9-136">具象実装`GreeterService`は、メソッドをオーバーライドし、grpc 呼び出しを処理するロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="e0ba9-137">クライアント側の資産の場合は、具象的なクライアントの種類が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="e0ba9-138">*Proto*ファイル内の grpc 呼び出しは、具象型のメソッドに変換されます。これは、を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="e0ba9-139">前に説明した例では、具象`GreeterClient`型が生成されています。 `greet.proto`</span><span class="sxs-lookup"><span data-stu-id="e0ba9-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="e0ba9-140">を`GreeterClient.SayHelloAsync`呼び出して、サーバーに対する grpc 呼び出しを開始します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="e0ba9-141">既定では、サーバーとクライアントの資産は、@no__t 2 つの項目グループに含まれる *@no__t*の各ファイルに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="e0ba9-142">サーバープロジェクト`GrpcServices`でサーバー資産のみが生成されるようにするには、属性を`Server`に設定します。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="e0ba9-143">同様に、クライアントプロジェクトでは`Client` 、属性はに設定されます。</span><span class="sxs-lookup"><span data-stu-id="e0ba9-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="e0ba9-144">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e0ba9-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
