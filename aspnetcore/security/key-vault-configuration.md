---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: rick-anderson
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d617627154e3125a6a59d082fd401fc69c25fcb3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652178"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core の構成プロバイダーの Azure Key Vault

By [Andrew Stanton-看護師](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。 ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御する。
* 構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

パッケージ[への参照を、パッケージへ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)の参照を追加します。

## <a name="sample-app"></a>サンプル アプリ

サンプルアプリは、 *Program.cs*ファイルの先頭にある `#define` ステートメントによって決定される2つのモードのいずれかで実行されます。

* `Certificate` &ndash; は、Azure Key Vault クライアント ID と x.509 証明書を使用して Azure Key Vault に格納されているシークレットにアクセスする方法を示しています。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed` &ndash; では、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するために、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証する方法について説明します。 マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。 このサンプルの `Managed` バージョンは、Azure にデプロイする必要があります。 「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。

プリプロセッサディレクティブ (`#define`) を使用してサンプルアプリケーションを構成する方法の詳細については、「<xref:index#preprocessor-directives-in-sample-code>」を参照してください。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットストレージ

[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。

Secret Manager ツールでは、アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが必要です。 プロパティ値 (`{GUID}`) を任意の一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション) では、 [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として `:` (コロン) を使用します。

シークレットマネージャーは、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に開かれているコマンドシェルから使用されます。ここで `{SECRET NAME}` は名前で、`{SECRET VALUE}` はの値です。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが[Azure Key Vault セクションを含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されている場合、`_dev` サフィックスは `_prod`に変更されます。 サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境のシークレットストレージ

クイックスタートに記載されている手順[: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli)する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。 詳細については、「」を参照してください。

1. [Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキストボックス内の検索文字列 "Azure CLI" を使用します。
   * **[Cloud Shell の起動]** ボタンを使用して、ブラウザーで Cloud Shell を開きます。
   * Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。

   詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。

1. まだ認証されていない場合は、`az login` コマンドを使用してサインインします。

1. 次のコマンドを使用してリソースグループを作成します。ここで `{RESOURCE GROUP NAME}` は、新しいリソースグループのリソースグループ名で、`{LOCATION}` は Azure リージョン (datacenter) です。

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで `{KEY VAULT NAME}` は新しいキーコンテナーの名前、`{LOCATION}` は Azure リージョン (datacenter) です。

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、キーコンテナーにシークレットを作成します。

   Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。 階層値 (構成セクション) は、区切り記号として `--` (2 つのダッシュ) を使用します。 [ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。 そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。

   サンプルアプリで使用するシークレットは次のとおりです。 値には、開発環境で読み込まれた `_dev` サフィックス値とユーザーシークレットから区別するための `_prod` サフィックスが含まれます。 `{KEY VAULT NAME}` は、前の手順で作成したキーコンテナーの名前に置き換えます。

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する

**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。 マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。

このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Certificate`に設定されている場合に、アプリケーション ID と x.509 証明書を使用します。

1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を作成します。 証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモしておきます。
1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を DER でエンコードされた証明書 ( *.cer*) としてエクスポートします。
1. アプリを Azure AD (**アプリの登録**) に登録します。
1. DER でエンコードされた証明書 ( *.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. **[証明書 & シークレット]** に移動します。
   1. 公開キーを含む証明書をアップロードするには、 **[証明書のアップロード]** を選択します。 *.Cer*、 *pem*、または *.crt*証明書を使用できます。
1. Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。
1. Azure portal の**キーコンテナー**に移動します。
1. Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。
1. **[アクセス ポリシー]** を選択します。
1. **[アクセスポリシーの追加]** を選択します。
1. **[シークレットのアクセス許可]** を開き、アプリに**Get**および**List**アクセス許可を提供します。
1. **[プリンシパルの選択]** を選択し、名前で登録済みのアプリを選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリをデプロイします。

`Certificate` サンプルアプリは、シークレット名と同じ名前の `IConfigurationRoot` から構成値を取得します。

* 非階層値: `SecretName` の値は `config["SecretName"]`で取得されます。
* 階層値 (セクション): `:` (コロン) 表記または `GetSection` 拡張メソッドを使用します。 次のいずれかの方法を使用して、構成値を取得します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは、appsettings ファイルによって指定さ*れ*た値を使用して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> を呼び出します。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

サンプル値:

* Key vault 名: `contosovault`
* アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値は `_dev` サフィックスで読み込まれます。 運用環境では、値は `_prod` サフィックスで読み込まれます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用する

**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。

このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Managed`に設定されている場合に、Azure リソースの管理対象 id を使用します。

アプリの*appsettings*ファイルにコンテナー名を入力します。 このサンプルアプリでは、`Managed` バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません。そのため、これらの構成エントリは無視してかまいません。 アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。

サンプルアプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。 次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。 オブジェクト ID は、App Service の **[id]** パネルの Azure portal に表示されます。

Azure CLI とアプリのオブジェクト ID を使用して、キーコンテナーにアクセスするための `list` と `get` アクセス許可をアプリに提供します。

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。

サンプルアプリ:

* 接続文字列を指定せずに `AzureServiceTokenProvider` クラスのインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。
* `AzureServiceTokenProvider` インスタンストークンのコールバックを使用して、新しい <xref:Microsoft.Azure.KeyVault.KeyVaultClient> が作成されます。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient> インスタンスは、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の既定の実装で使用されます。この実装では、すべてのシークレット値が読み込まれ、二重ダッシュ (`--`) がキー名のコロン (`:`) で置き換えられます。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Key vault 名の例の値: `contosovault`
    
*appsettings.json*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値はユーザーシークレットによって提供されるため、`_dev` のサフィックスが付いています。 運用環境では、値は Azure Key Vault によって提供されるため、`_prod` のサフィックスを使用して読み込まれます。

`Access denied` エラーが発生した場合は、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認してください。 Azure でサービスを再起動したことを確認します。

マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「[管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。

## <a name="configuration-options"></a>構成オプション

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>を受け入れることができます。

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| プロパティ         | 説明 |
| ---------------- | ----------- |
| `Client`         | 値を取得するために使用する <xref:Microsoft.Azure.KeyVault.KeyVaultClient>。 |
| `Manager`        | シークレットの読み込みを制御するために使用される <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> インスタンス。 |
| `ReloadInterval` | キーコンテナーのポーリングによって変更が試行されるまでの待機時間を `Timespan` します。 既定値は `null` です (構成は再読み込みされません)。 |
| `Vault`          | Key vault URI。 |

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用する

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されています。これを使用すると、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。 さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。

次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) で `5000-AppSecret` (キーコンテナーのシークレット名にはピリオドは使用できません) にシークレットが設定されています。 このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。 5\.1.0.0 の別のバージョンのアプリでは、キーコンテナーにシークレット (およびシークレットマネージャーツールを使用) が `5100-AppSecret`に追加されます。 各アプリバージョンでは、バージョン管理されているシークレット値が `AppSecret`として構成に読み込まれ、シークレットが読み込まれるときにバージョンが除去されます。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタム <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>を使用して呼び出されます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の実装は、構成に適切なシークレットを読み込むために、シークレットのバージョンプレフィックスに反応します。

* `Load` は、名前がプレフィックスで始まるときにシークレットを読み込みます。 他のシークレットは読み込まれていません。
* `GetKey`:
  * シークレット名からプレフィックスを削除します。
  * 任意の名前の2つのダッシュを `KeyDelimiter`に置き換えます。これは、構成で使用される区切り記号 (通常はコロン) です。 Azure Key Vault では、シークレット名にコロンを使用することはできません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを見つけるプロバイダーアルゴリズムによって呼び出されます。 `Load`でバージョンプレフィックスが見つかった場合、アルゴリズムは、`GetKey` メソッドを使用してシークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクトファイルで指定されているアプリのバージョン。 次の例では、アプリのバージョンが `5.0.0.0`に設定されています。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが存在することを確認します。 `{GUID}` はユーザー指定の GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault シークレットが読み込まれます。 `5000-AppSecret` の文字列シークレットは、アプリのプロジェクトファイル (`5.0.0.0`) で指定されているアプリのバージョンと一致します。

1. `5000` バージョン (ダッシュ付き) がキー名から削除されます。 アプリ全体で、キー `AppSecret` 使用して構成を読み取り、シークレット値を読み込みます。

1. アプリのバージョンがプロジェクトファイルで `5.1.0.0` に変更され、アプリが再度実行された場合、返されたシークレット値は開発環境で `5.1.0.0_secret_value_dev` され、運用環境で `5.1.0.0_secret_value_prod` ます。

> [!NOTE]
> 独自の <xref:Microsoft.Azure.KeyVault.KeyVaultClient> の実装を提供して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>することもできます。 カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。

キーがコロン (`:`) 区切り記号を含むように構成ソースから読み取る場合、配列を構成するキー (`:0:`、`:1:`、&hellip; `:{n}:`) を区別するために、数値キーセグメントが使用されます。 詳細については、「[構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。 このトピックで説明する方法では、階層値 (セクション) の区切り記号として二重ダッシュ (`--`) を使用します。 配列キーは、二重ダッシュと数値キーセグメント (`--0--`、`--1--`、&hellip; `--{n}--`) を使用して Azure Key Vault に格納されます。

JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。 `WriteTo` 配列に定義されている2つのオブジェクトリテラルは、ログ出力の宛先を示す2つの Serilog*シンク*を反映しています。

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

前の JSON ファイルに示されている構成は、二重ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | 値 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは `IConfigurationRoot.Reload()` が呼び出されるまでキャッシュされます。 `Reload` が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットはアプリによって尊重されません。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効なシークレットと期限切れのシークレット

無効で期限切れのシークレットは、<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>をスローします。 アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の状況では、構成を読み込むことができません。

* Azure Active Directory で、アプリまたは証明書が正しく構成されていません。
* キーコンテナーは Azure Key Vault に存在しません。
* アプリは、key vault にアクセスする権限がありません。
* アクセスポリシーには、`Get` および `List` のアクセス許可は含まれません。
* Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。
* アプリの key vault 名 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、Azure AD 証明書の拇印 (`AzureADCertThumbprint`) が正しくありません。
* 読み込みようとしている値の構成キー (名前) が正しくありません。
* アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で **[保存]** ボタンが選択されていませんでした。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイックスタート: .NET web アプリを使用して Azure Key Vault からシークレットを設定および取得する](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンと共に Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。 ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御する。
* 構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

パッケージ[への参照を、パッケージへ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)の参照を追加します。

## <a name="sample-app"></a>サンプル アプリ

サンプルアプリは、 *Program.cs*ファイルの先頭にある `#define` ステートメントによって決定される2つのモードのいずれかで実行されます。

* `Certificate` &ndash; は、Azure Key Vault クライアント ID と x.509 証明書を使用して Azure Key Vault に格納されているシークレットにアクセスする方法を示しています。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed` &ndash; では、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するために、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証する方法について説明します。 マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。 このサンプルの `Managed` バージョンは、Azure にデプロイする必要があります。 「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。

プリプロセッサディレクティブ (`#define`) を使用してサンプルアプリケーションを構成する方法の詳細については、「<xref:index#preprocessor-directives-in-sample-code>」を参照してください。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットストレージ

[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。

Secret Manager ツールでは、アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが必要です。 プロパティ値 (`{GUID}`) を任意の一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション) では、 [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として `:` (コロン) を使用します。

シークレットマネージャーは、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に開かれているコマンドシェルから使用されます。ここで `{SECRET NAME}` は名前で、`{SECRET VALUE}` はの値です。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが[Azure Key Vault セクションを含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されている場合、`_dev` サフィックスは `_prod`に変更されます。 サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境のシークレットストレージ

クイックスタートに記載されている手順[: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli)する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。 詳細については、「」を参照してください。

1. [Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキストボックス内の検索文字列 "Azure CLI" を使用します。
   * **[Cloud Shell の起動]** ボタンを使用して、ブラウザーで Cloud Shell を開きます。
   * Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。

   詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。

1. まだ認証されていない場合は、`az login` コマンドを使用してサインインします。

1. 次のコマンドを使用してリソースグループを作成します。ここで `{RESOURCE GROUP NAME}` は、新しいリソースグループのリソースグループ名で、`{LOCATION}` は Azure リージョン (datacenter) です。

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで `{KEY VAULT NAME}` は新しいキーコンテナーの名前、`{LOCATION}` は Azure リージョン (datacenter) です。

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、キーコンテナーにシークレットを作成します。

   Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。 階層値 (構成セクション) は、区切り記号として `--` (2 つのダッシュ) を使用します。 [ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。 そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。

   サンプルアプリで使用するシークレットは次のとおりです。 値には、開発環境で読み込まれた `_dev` サフィックス値とユーザーシークレットから区別するための `_prod` サフィックスが含まれます。 `{KEY VAULT NAME}` は、前の手順で作成したキーコンテナーの名前に置き換えます。

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する

**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。 マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。

このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Certificate`に設定されている場合に、アプリケーション ID と x.509 証明書を使用します。

1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を作成します。 証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモしておきます。
1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を DER でエンコードされた証明書 ( *.cer*) としてエクスポートします。
1. アプリを Azure AD (**アプリの登録**) に登録します。
1. DER でエンコードされた証明書 ( *.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. **[証明書 & シークレット]** に移動します。
   1. 公開キーを含む証明書をアップロードするには、 **[証明書のアップロード]** を選択します。 *.Cer*、 *pem*、または *.crt*証明書を使用できます。
1. Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。
1. Azure portal の**キーコンテナー**に移動します。
1. Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。
1. **[アクセス ポリシー]** を選択します。
1. **[アクセスポリシーの追加]** を選択します。
1. **[シークレットのアクセス許可]** を開き、アプリに**Get**および**List**アクセス許可を提供します。
1. **[プリンシパルの選択]** を選択し、名前で登録済みのアプリを選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリをデプロイします。

`Certificate` サンプルアプリは、シークレット名と同じ名前の `IConfigurationRoot` から構成値を取得します。

* 非階層値: `SecretName` の値は `config["SecretName"]`で取得されます。
* 階層値 (セクション): `:` (コロン) 表記または `GetSection` 拡張メソッドを使用します。 次のいずれかの方法を使用して、構成値を取得します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは、appsettings ファイルによって指定さ*れ*た値を使用して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> を呼び出します。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

サンプル値:

* Key vault 名: `contosovault`
* アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値は `_dev` サフィックスで読み込まれます。 運用環境では、値は `_prod` サフィックスで読み込まれます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用する

**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。

このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Managed`に設定されている場合に、Azure リソースの管理対象 id を使用します。

アプリの*appsettings*ファイルにコンテナー名を入力します。 このサンプルアプリでは、`Managed` バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません。そのため、これらの構成エントリは無視してかまいません。 アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。

サンプルアプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。 次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。 オブジェクト ID は、App Service の **[id]** パネルの Azure portal に表示されます。

Azure CLI とアプリのオブジェクト ID を使用して、キーコンテナーにアクセスするための `list` と `get` アクセス許可をアプリに提供します。

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。

サンプルアプリ:

* 接続文字列を指定せずに `AzureServiceTokenProvider` クラスのインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。
* `AzureServiceTokenProvider` インスタンストークンのコールバックを使用して、新しい <xref:Microsoft.Azure.KeyVault.KeyVaultClient> が作成されます。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient> インスタンスは、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の既定の実装で使用されます。この実装では、すべてのシークレット値が読み込まれ、二重ダッシュ (`--`) がキー名のコロン (`:`) で置き換えられます。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Key vault 名の例の値: `contosovault`
    
*appsettings.json*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値はユーザーシークレットによって提供されるため、`_dev` のサフィックスが付いています。 運用環境では、値は Azure Key Vault によって提供されるため、`_prod` のサフィックスを使用して読み込まれます。

`Access denied` エラーが発生した場合は、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認してください。 Azure でサービスを再起動したことを確認します。

マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「[管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用する

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されています。これを使用すると、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。 さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。

次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) で `5000-AppSecret` (キーコンテナーのシークレット名にはピリオドは使用できません) にシークレットが設定されています。 このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。 5\.1.0.0 の別のバージョンのアプリでは、キーコンテナーにシークレット (およびシークレットマネージャーツールを使用) が `5100-AppSecret`に追加されます。 各アプリバージョンでは、バージョン管理されているシークレット値が `AppSecret`として構成に読み込まれ、シークレットが読み込まれるときにバージョンが除去されます。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタム <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>を使用して呼び出されます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の実装は、構成に適切なシークレットを読み込むために、シークレットのバージョンプレフィックスに反応します。

* `Load` は、名前がプレフィックスで始まるときにシークレットを読み込みます。 他のシークレットは読み込まれていません。
* `GetKey`:
  * シークレット名からプレフィックスを削除します。
  * 任意の名前の2つのダッシュを `KeyDelimiter`に置き換えます。これは、構成で使用される区切り記号 (通常はコロン) です。 Azure Key Vault では、シークレット名にコロンを使用することはできません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを見つけるプロバイダーアルゴリズムによって呼び出されます。 `Load`でバージョンプレフィックスが見つかった場合、アルゴリズムは、`GetKey` メソッドを使用してシークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクトファイルで指定されているアプリのバージョン。 次の例では、アプリのバージョンが `5.0.0.0`に設定されています。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが存在することを確認します。 `{GUID}` はユーザー指定の GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault シークレットが読み込まれます。 `5000-AppSecret` の文字列シークレットは、アプリのプロジェクトファイル (`5.0.0.0`) で指定されているアプリのバージョンと一致します。

1. `5000` バージョン (ダッシュ付き) がキー名から削除されます。 アプリ全体で、キー `AppSecret` 使用して構成を読み取り、シークレット値を読み込みます。

1. アプリのバージョンがプロジェクトファイルで `5.1.0.0` に変更され、アプリが再度実行された場合、返されたシークレット値は開発環境で `5.1.0.0_secret_value_dev` され、運用環境で `5.1.0.0_secret_value_prod` ます。

> [!NOTE]
> 独自の <xref:Microsoft.Azure.KeyVault.KeyVaultClient> の実装を提供して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>することもできます。 カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。

キーがコロン (`:`) 区切り記号を含むように構成ソースから読み取る場合、配列を構成するキー (`:0:`、`:1:`、&hellip; `:{n}:`) を区別するために、数値キーセグメントが使用されます。 詳細については、「[構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。 このトピックで説明する方法では、階層値 (セクション) の区切り記号として二重ダッシュ (`--`) を使用します。 配列キーは、二重ダッシュと数値キーセグメント (`--0--`、`--1--`、&hellip; `--{n}--`) を使用して Azure Key Vault に格納されます。

JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。 `WriteTo` 配列に定義されている2つのオブジェクトリテラルは、ログ出力の宛先を示す2つの Serilog*シンク*を反映しています。

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

前の JSON ファイルに示されている構成は、二重ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | 値 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは `IConfigurationRoot.Reload()` が呼び出されるまでキャッシュされます。 `Reload` が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットはアプリによって尊重されません。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効なシークレットと期限切れのシークレット

無効で期限切れのシークレットは、<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>をスローします。 アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の状況では、構成を読み込むことができません。

* Azure Active Directory で、アプリまたは証明書が正しく構成されていません。
* キーコンテナーは Azure Key Vault に存在しません。
* アプリは、key vault にアクセスする権限がありません。
* アクセスポリシーには、`Get` および `List` のアクセス許可は含まれません。
* Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。
* アプリの key vault 名 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、Azure AD 証明書の拇印 (`AzureADCertThumbprint`) が正しくありません。
* 読み込みようとしている値の構成キー (名前) が正しくありません。
* アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で **[保存]** ボタンが選択されていませんでした。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイックスタート: .NET web アプリを使用して Azure Key Vault からシークレットを設定および取得する](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンと共に Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

