---
title: Azure App Service に ASP.NET Core SignalRアプリを発行する
author: bradygaster
description: ASP.NET Core SignalRアプリを Azure App Service に発行する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: a5d19c1519c69351605e8da1d8fa70bff784efd4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777190"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>ASP.NET Core SignalR アプリを Azure App Service に発行する

[Brady](https://twitter.com/bradygaster)による

[Azure App Service](/azure/app-service/app-service-web-overview)は、web アプリ (ASP.NET Core を含む) をホストするための[Microsoft クラウドコンピューティング](https://azure.microsoft.com/)プラットフォームサービスです。

> [!NOTE]
> この記事では、Visual Studio から ASP.NET Core SignalR アプリを発行する方法について説明します。 詳細については、「 [SignalR service For Azure](https://azure.microsoft.com/services/signalr-service)」を参照してください。

## <a name="publish-the-app"></a>アプリの発行

この記事では、Visual Studio のツールを使用した発行について説明します。 Visual Studio Code ユーザーは、 [Azure CLI](/cli/azure)のコマンドを使用して Azure にアプリを発行できます。 詳細については、「[コマンドラインツールを使用した Azure への ASP.NET Core アプリの発行](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、**[発行]** を選択します。

1. [**発行先の選択**] ダイアログで [ **App Service**と**新規作成**] が選択されていることを確認します。

1. [**発行**] ボタンドロップダウンから [**プロファイルの作成**] を選択します。

   [ **App Service の作成**] ダイアログで、次の表に記載されている情報を入力し、[**作成**] を選択します。

   | Item               | 説明 |
   | ------------------ | ----------- |
   | **名前**           | アプリの一意の名前。 |
   | **サブスクリプション**   | アプリが使用する Azure サブスクリプション。 |
   | **リソース グループ** | アプリが所属する関連リソースのグループ。 |
   | **ホスティング プラン**   | Web アプリの料金プラン。 |

1. [**依存関係** > の**追加**] ドロップダウンリストで**Azure SignalRサービス**を選択します。

   ![[追加] ドロップダウンリストでSignalRの Azure サービスの選択を示す [依存関係] 領域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. [ ** SignalR Azure サービス**] ダイアログで、[**新しい azure SignalRサービスインスタンスの作成**] を選択します。

1. **名前**、**リソースグループ**、および**場所**を指定します。 [ ** SignalR Azure サービス**] ダイアログに戻り、[**追加**] を選択します。

Visual Studio は次のタスクを完了します。

* 発行設定を含む発行プロファイルを作成します。
* 指定された詳細を使用して*Azure Web アプリ*を作成します。
* アプリを発行します。
* ブラウザーを起動して、web アプリを読み込みます。

アプリの URL の形式は`{APP SERVICE NAME}.azurewebsites.net`です。 たとえば、という名前`SignalRChatApp`のアプリの`https://signalrchatapp.azurewebsites.net`URL はです。

Preview .NET Core リリースを対象とするアプリをデプロイするときに HTTP *502.2-Bad Gateway*エラーが発生する場合は、「 [ASP.NET Core プレビューリリース Azure App Service をデプロイ](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)して解決するには」を参照してください。

## <a name="configure-the-app-in-azure-app-service"></a>Azure App Service でアプリを構成する

> [!NOTE]
> *このセクションは、Azure SignalRサービスを使用していないアプリにのみ適用されます。*
>
> アプリで Azure SignalRサービスを使用する場合、App Service では、このセクションで説明するアプリケーション要求ルーティング (ARR) アフィニティと Web ソケットの構成は必要ありません。 クライアントは、アプリに直接ではSignalRなく、Azure サービスに Web ソケットを接続します。

Azure SignalRサービスを使用せずにホストされるアプリの場合は、次を有効にします。

* [ARR アフィニティ](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)は、ユーザーから同じ App Service インスタンスに要求をルーティングします。 既定の設定は**On**です。
* Web ソケットトランスポートを機能させるための[Web ソケット](xref:fundamentals/websockets)。 既定の設定は [**オフ**] です。

1. Azure portal で、 **App Services**内の web アプリに移動します。
1. **構成** > の**全般設定**を開きます。
1. **Web ソケット**を**オン**に設定します。
1. **ARR affinity**が**On**に設定されていることを確認します。

## <a name="app-service-plan-limits"></a>App Service プランの制限

Web ソケットとその他のトランスポートは、選択した App Service プランに基づいて制限されます。 詳細については、azure[サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits#app-service-limits)に関する記事の*azure Cloud Services の制限*と*App Service の制限事項*に関するセクションを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [Azure SignalRサービスとは](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを公開する](/azure/app-service/app-service-web-get-started-dotnet)
* [Azure で ASP.NET Core プレビューアプリをホストしてデプロイする](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
