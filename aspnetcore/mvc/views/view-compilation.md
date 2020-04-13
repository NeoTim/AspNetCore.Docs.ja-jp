---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223960"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core での Razor ファイルのコンパイル

著者 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。 実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。

## <a name="razor-compilation"></a>Razor コンパイル

Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。 有効になっていると、実行時コンパイルはビルド時のコンパイルを補完し、編集された Razor ファイルを更新できます。

## <a name="runtime-compilation"></a>実行時のコンパイル

すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。

1. パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。 次に例を示します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>実行時コンパイルを条件付きで有効にする

実行時コンパイルをローカル開発でのみ有効にできます。 このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。

* コンパイル済みのビューを使用する。
* サイズが小さい。
* 運用環境のファイル監視を有効にしない。

環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。

1. アクティブな `Configuration` 値に基づいて、[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) パッケージを条件付きで参照します。

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。 `ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>その他の技術情報

* [プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* プロジェクト間[でのランタイム コンパイル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)の動作を示すサンプルについては、GitHub のランタイム コンパイル のサンプルを参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。 Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。

## <a name="razor-compilation"></a>Razor コンパイル

Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。 更新後の Razor ファイルの編集は、ビルド時にサポートされています。 既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。

> [!IMPORTANT]
> プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。 [Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。
>
> Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。 たとえば、*.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。

## <a name="runtime-compilation"></a>実行時のコンパイル

ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。 ASP.NET Core MVC は、*.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。

## <a name="additional-resources"></a>その他の技術情報

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
