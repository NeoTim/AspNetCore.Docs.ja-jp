---
title: ASP.NET Core でファイルをアップロードする
author: rick-anderson
description: モデル バインドとストリーミングを使用して、ASP.NET Core MVC でファイルをアップロードする方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: fc71c39dd1aa70e6b092799fec00bd7bf66703e8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654086"
---
# <a name="upload-files-in-aspnet-core"></a>ASP.NET Core でファイルをアップロードする

[上田氏](https://ardalis.com/)との[嵐](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。 攻撃者が次のようなことを試みる可能性があります。

* [サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。
* ウイルスまたはマルウェアをアップロードする。
* ネットワークやサーバーを他の方法で侵害する。

攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。

* 専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。 専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。 ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;
* アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;
* アプリによって決められた安全なファイル名を使用します。 ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。&dagger; HTML では、表示時に信頼されていないファイル名をエンコードします。 たとえば、ファイル名をログに記録したり、UI に表示したりします (Razor では、出力が自動的に HTML エンコードされます)。
* アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* クライアント側のチェックがサーバーで実行されていることを確認します。&dagger; クライアント側のチェックは簡単に回避できます。
* アップロードされたファイルのサイズをチェックします。 サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;
* 同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。
* **ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**

&dagger;サンプル アプリで、条件を満たす方法が示されています。

> [!WARNING]
> システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。
>
> * システムの制御を完全に掌握する。
> * システムがクラッシュする結果で、システムを過負荷状態にする。
> * ユーザーまたはシステムのデータを破壊する。
> * パブリック UI に落書きする。
>
> ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。
>
> * [Unrestricted File Upload (ファイルの無制限のアップロード)](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。

## <a name="storage-scenarios"></a>ストレージのシナリオ

ファイルの一般的なストレージ オプションには次のようなものがあります。

* ［データベース］

  * 小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。
  * 多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。
  * データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。

* 物理ストレージ (ファイル システムまたはネットワーク共有)

  * 大きいファイルのアップロードの場合:
    * データベースの制限によって、アップロードのサイズが制限される場合があります。
    * 多くの場合、物理ストレージはデータベース内のストレージより高コストです。
  * 物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。
  * アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。 **実行アクセス許可は付与しないでください。**

* データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * 通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。
  * 大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。

  詳細については、「[クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。

## <a name="file-upload-scenarios"></a>ファイル アップロードのシナリオ

ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。

**バッファーリング**

ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。

ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。 アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。 ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。

> [!NOTE]
> 1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。

小さいファイルのバッファーリングについては、後のセクションで説明します。

* [物理ストレージ](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)

**ストリーミング**

ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。 ストリーミングによってパフォーマンスが大幅に向上することはありません。 ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。

大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする

小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。

次の例では、Razor Pages フォームを使用して 1 つのファイル (サンプル アプリの*Pages/BufferedSingleFileUploadPhysical.cshtml*) をアップロードする方法を示します。

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

次の例は前の例と似ていますが、以下の点が異なります。

* JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。
* 検証は行われません。

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。

* Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。
* `XMLHttpRequest`を使用します。 例 :

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

ファイルのアップロードをサポートするには、HTML フォームで `enctype` のエンコード タイプ (`multipart/form-data`) を指定する必要があります。

`files` 入力要素で複数のファイルのアップロードをサポートするには、`multiple` 要素で `<input>` 属性を指定します。

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

サーバーにアップロードされた個々のファイルには、[ を使用して](xref:mvc/models/model-binding)モデル バインド<xref:Microsoft.AspNetCore.Http.IFormFile>でアクセスできます。 サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。

<a name="filename"></a>

> [!WARNING]
> **の** プロパティは、表示とログ記録の目的以外に使用`FileName`しないでください<xref:Microsoft.AspNetCore.Http.IFormFile>。 表示またはログ記録を行うときに、ファイル名を HTML エンコードします。 攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。 アプリケーションで次の処理を行う必要があります。
>
> * ユーザーが指定したファイル名からパスを削除します。
> * UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。
> * ストレージ用に新しいランダムなファイル名を生成します。
>
> 次のコードでは、ファイル名からパスを削除します。
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> これまでに示した例では、セキュリティ上の考慮事項については考えられていません。 以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。
>
> * [セキュリティに関する考慮事項](#security-considerations)
> * [検証](#validation)

モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。

* 1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。
* 複数のファイルを表す次のいずれかのコレクション:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> バインドでは、名前でフォーム ファイルが照合されます。 たとえば、HTML の `name` の `<input type="file" name="formFile">` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。 詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。

次に例を示します。

* アップロードされた 1 つ以上のファイルをループします。
* [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。 
* アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。
* アップロードされたファイルの合計数とサイズを返します。

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。 次の例では、構成からパスを取得します。

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<xref:System.IO.FileStream> に渡すパスには、ファイル名が含まれている "*必要があります*"。 ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。

<xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。 アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。 ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。

アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。

> [!WARNING]
> 以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。 65,535 ファイルの制限は、サーバーごとの制限です。 Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。
>
> * [GetTempFileNameA 関数](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする

[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。 前の例では、バインドされたモデル プロパティが使用されています。

`FileUpload` は、Razor Pages フォームで使用されます。

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。 次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

前の例は、次のサンプル アプリで示されているシナリオに似ています。

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。
>
> 検証を行わずに `FileName` の <xref:Microsoft.AspNetCore.Http.IFormFile> プロパティに依存したり、信頼したりしないでください。 `FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。
>
> 示した例では、セキュリティ上の考慮事項については考えられていません。 以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。
>
> * [セキュリティに関する考慮事項](#security-considerations)
> * [検証](#validation)

### <a name="upload-large-files-with-streaming"></a>ストリーミングを使用して大きいファイルをアップロードする

次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。 ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。 アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。 アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。 マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。

最初のページ応答ではフォームが読み込まれ、Cookie に偽造防止トークンが保存されます (`GenerateAntiforgeryTokenCookieAttribute` 属性を使用)。 その属性では、ASP.NET Core の組み込みの[偽造防止サポート](xref:security/anti-request-forgery)を使用して、要求トークンで Cookie が設定されます。

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

サンプル アプリでは、`GenerateAntiforgeryTokenCookieAttribute` および `DisableFormValueModelBindingAttribute` は、`/StreamedSingleFileUploadDb`Razor Pages の規則`/StreamedSingleFileUploadPhysical`を使用して、`Startup.ConfigureServices` で [ および ](xref:razor-pages/razor-pages-conventions) のページ アプリケーション モデルにフィルターとして適用されます。

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。 アクション メソッドでは、`Request` プロパティが直接操作されます。 `MultipartReader` は各セクションを読み取るために使用されます。 キー/値データは `KeyValueAccumulator` に格納されます。 マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。

EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。

## <a name="validation"></a>Validation

サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。 サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、`ProcessFormFile`Utilities/FileHelpers.cs*ファイルの* メソッドを参照してください。 ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。

> [!WARNING]
> サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。 ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。
>
> このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。
>
> * 実装を完全に理解している。
> * アプリの環境と仕様に合わせて実装が適切に変更されている。
>
> **これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**

### <a name="content-validation"></a>コンテンツの検証

**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**

大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。 ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。 通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。 合格したファイルは、通常のファイル ストレージの場所に移動されます。 これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。 このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。

### <a name="file-extension-validation"></a>ファイル拡張子の検証

アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。 例 :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>ファイルのシグネチャの検証

ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。 これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。 サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。 次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。

### <a name="file-name-security"></a>ファイル名のセキュリティ

ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。

Razor では、プロパティ値が表示用に自動的に HTML エンコードされます。 次のコードは安全に使用できます。

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor の外部では、ユーザーの要求からのファイル名の内容を常に <xref:System.Net.WebUtility.HtmlEncode*> でエンコードします。

多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。 アプリの仕様を満たす追加のロジックを提供します。

### <a name="size-validation"></a>サイズの検証

アップロードされるファイルのサイズを制限します。

サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。 その制限は、[appsettings.json](xref:fundamentals/configuration/index) ファイルの *Configuration* によって提供されます。

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` が `PageModel` クラスに挿入されます。

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

ファイルのサイズが制限を超えると、ファイルは拒否されます。

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>name 属性の値を POST メソッドのパラメーター名に一致させる

フォーム データを POST する、または JavaScript の `FormData` を直接使用する、Razor 以外のフォームでは、フォームの要素または `FormData` で指定されている名前が、コントローラーのアクションのパラメーターの name と一致している必要があります。

たとえば、行が次のように表示されているとします。

* `<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。

* `Upload` という名前の Razor Pages ページ ハンドラー メソッドの場合:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST コントローラー アクション メソッドの場合:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>サーバーとアプリの構成

### <a name="multipart-body-length-limit"></a>マルチパート本文の長さの制限

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。 この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。 既定値は 134,217,728 (128 MB) です。 制限をカスタマイズするには、<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> の設定 `Startup.ConfigureServices` を使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を設定するには、<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。

Razor Pages アプリでは、[ の ](xref:razor-pages/razor-pages-conventions)convention`Startup.ConfigureServices` を使用してフィルターを適用します。

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

Razor Pages アプリまたは MVC アプリでは、ページ モデルまたはアクション メソッドにフィルターを適用します。

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel の最大要求本文サイズ

Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。 制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>MaxRequestBodySize[ を設定するには、](xref:fundamentals/servers/kestrel#maximum-request-body-size) を使用します。

Razor Pages アプリでは、[ の ](xref:razor-pages/razor-pages-conventions)convention`Startup.ConfigureServices` を使用してフィルターを適用します。

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

Razor Pages アプリまたは MVC アプリでは、ページ ハンドラー クラスまたはアクション メソッドにフィルターを適用します。

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute` は、[`@attribute`](xref:mvc/views/razor#attribute) Razor ディレクティブを使用して適用することもできます。

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>その他の Kestrel の制限

Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。

* [クライアントの最大接続数](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [要求と応答のデータ レート](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS の内容の長さの制限

既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。 *web.config* ファイルで制限をカスタマイズします。

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

この設定は IIS にのみ適用されます。 Kestrel でホストする場合、既定ではこの動作は発生しません。 詳細については、「[要求制限 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。

ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。 詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>IIS サーバーに展開したときの "見つかりません" エラー

次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。

### <a name="connection-failure"></a>接続エラー

接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。 詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。 Kestrel クライアント接続の制限の調整も必要な場合があります。

### <a name="null-reference-exception-with-iformfile"></a>IFormFile での null 参照例外

コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `enctype` に値 `multipart/form-data` が指定されていることを確認します。 この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。 また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。

### <a name="stream-was-too-long"></a>ストリームが長すぎる

このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。 `MemoryStream` のサイズ制限は `int.MaxValue` です。 アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。 攻撃者が次のようなことを試みる可能性があります。

* [サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。
* ウイルスまたはマルウェアをアップロードする。
* ネットワークやサーバーを他の方法で侵害する。

攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。

* 専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。 専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。 ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;
* アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;
* アプリによって決められた安全なファイル名を使用します。 ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。&dagger; HTML では、表示時に信頼されていないファイル名をエンコードします。 たとえば、ファイル名をログに記録したり、UI に表示したりします (Razor では、出力が自動的に HTML エンコードされます)。
* アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* クライアント側のチェックがサーバーで実行されていることを確認します。&dagger; クライアント側のチェックは簡単に回避できます。
* アップロードされたファイルのサイズをチェックします。 サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;
* 同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。
* **ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**

&dagger;サンプル アプリで、条件を満たす方法が示されています。

> [!WARNING]
> システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。
>
> * システムの制御を完全に掌握する。
> * システムがクラッシュする結果で、システムを過負荷状態にする。
> * ユーザーまたはシステムのデータを破壊する。
> * パブリック UI に落書きする。
>
> ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。
>
> * [Unrestricted File Upload (ファイルの無制限のアップロード)](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。

## <a name="storage-scenarios"></a>ストレージのシナリオ

ファイルの一般的なストレージ オプションには次のようなものがあります。

* ［データベース］

  * 小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。
  * 多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。
  * データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。

* 物理ストレージ (ファイル システムまたはネットワーク共有)

  * 大きいファイルのアップロードの場合:
    * データベースの制限によって、アップロードのサイズが制限される場合があります。
    * 多くの場合、物理ストレージはデータベース内のストレージより高コストです。
  * 物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。
  * アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。 **実行アクセス許可は付与しないでください。**

* データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * 通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。
  * 大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。

  詳細については、「[クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。 そのトピックでは <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> が示されていますが、<xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> を使用する場合は、<xref:System.IO.FileStream> を使用して <xref:System.IO.Stream> を Blob Storage に保存することもできます。

## <a name="file-upload-scenarios"></a>ファイル アップロードのシナリオ

ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。

**バッファーリング**

ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。

ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。 アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。 ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。

> [!NOTE]
> 1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。

小さいファイルのバッファーリングについては、後のセクションで説明します。

* [物理ストレージ](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)

**ストリーミング**

ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。 ストリーミングによってパフォーマンスが大幅に向上することはありません。 ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。

大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする

小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。

次の例では、Razor Pages フォームを使用して 1 つのファイル (サンプル アプリの*Pages/BufferedSingleFileUploadPhysical.cshtml*) をアップロードする方法を示します。

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

次の例は前の例と似ていますが、以下の点が異なります。

* JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。
* 検証は行われません。

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。

* Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。
* `XMLHttpRequest`を使用します。 例 :

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

ファイルのアップロードをサポートするには、HTML フォームで `enctype` のエンコード タイプ (`multipart/form-data`) を指定する必要があります。

`files` 入力要素で複数のファイルのアップロードをサポートするには、`multiple` 要素で `<input>` 属性を指定します。

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

サーバーにアップロードされた個々のファイルには、[ を使用して](xref:mvc/models/model-binding)モデル バインド<xref:Microsoft.AspNetCore.Http.IFormFile>でアクセスできます。 サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。

<a name="filename2"></a>

> [!WARNING]
> **の** プロパティは、表示とログ記録の目的以外に使用`FileName`しないでください<xref:Microsoft.AspNetCore.Http.IFormFile>。 表示またはログ記録を行うときに、ファイル名を HTML エンコードします。 攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。 アプリケーションで次の処理を行う必要があります。
>
> * ユーザーが指定したファイル名からパスを削除します。
> * UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。
> * ストレージ用に新しいランダムなファイル名を生成します。
>
> 次のコードでは、ファイル名からパスを削除します。
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> これまでに示した例では、セキュリティ上の考慮事項については考えられていません。 以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。
>
> * [セキュリティに関する考慮事項](#security-considerations)
> * [検証](#validation)

モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。

* 1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。
* 複数のファイルを表す次のいずれかのコレクション:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> バインドでは、名前でフォーム ファイルが照合されます。 たとえば、HTML の `name` の `<input type="file" name="formFile">` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。 詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。

次に例を示します。

* アップロードされた 1 つ以上のファイルをループします。
* [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。 
* アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。
* アップロードされたファイルの合計数とサイズを返します。

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。 次の例では、構成からパスを取得します。

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<xref:System.IO.FileStream> に渡すパスには、ファイル名が含まれている "*必要があります*"。 ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。

<xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。 アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。 ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。

アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。

> [!WARNING]
> 以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。 65,535 ファイルの制限は、サーバーごとの制限です。 Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。
>
> * [GetTempFileNameA 関数](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする

[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。 前の例では、バインドされたモデル プロパティが使用されています。

`FileUpload` は、Razor Pages フォームで使用されます。

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。 次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

前の例は、次のサンプル アプリで示されているシナリオに似ています。

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。
>
> 検証を行わずに `FileName` の <xref:Microsoft.AspNetCore.Http.IFormFile> プロパティに依存したり、信頼したりしないでください。 `FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。
>
> 示した例では、セキュリティ上の考慮事項については考えられていません。 以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。
>
> * [セキュリティに関する考慮事項](#security-considerations)
> * [検証](#validation)

### <a name="upload-large-files-with-streaming"></a>ストリーミングを使用して大きいファイルをアップロードする

次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。 ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。 アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。 アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。 マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。

最初のページ応答ではフォームが読み込まれ、Cookie に偽造防止トークンが保存されます (`GenerateAntiforgeryTokenCookieAttribute` 属性を使用)。 その属性では、ASP.NET Core の組み込みの[偽造防止サポート](xref:security/anti-request-forgery)を使用して、要求トークンで Cookie が設定されます。

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

サンプル アプリでは、`GenerateAntiforgeryTokenCookieAttribute` および `DisableFormValueModelBindingAttribute` は、`/StreamedSingleFileUploadDb`Razor Pages の規則`/StreamedSingleFileUploadPhysical`を使用して、`Startup.ConfigureServices` で [ および ](xref:razor-pages/razor-pages-conventions) のページ アプリケーション モデルにフィルターとして適用されます。

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。 アクション メソッドでは、`Request` プロパティが直接操作されます。 `MultipartReader` は各セクションを読み取るために使用されます。 キー/値データは `KeyValueAccumulator` に格納されます。 マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。

EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。

## <a name="validation"></a>Validation

サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。 サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、`ProcessFormFile`Utilities/FileHelpers.cs*ファイルの* メソッドを参照してください。 ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。

> [!WARNING]
> サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。 ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。
>
> このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。
>
> * 実装を完全に理解している。
> * アプリの環境と仕様に合わせて実装が適切に変更されている。
>
> **これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**

### <a name="content-validation"></a>コンテンツの検証

**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**

大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。 ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。 通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。 合格したファイルは、通常のファイル ストレージの場所に移動されます。 これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。 このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。

### <a name="file-extension-validation"></a>ファイル拡張子の検証

アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。 例 :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>ファイルのシグネチャの検証

ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。 これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。 サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。 次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。

### <a name="file-name-security"></a>ファイル名のセキュリティ

ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。

Razor では、プロパティ値が表示用に自動的に HTML エンコードされます。 次のコードは安全に使用できます。

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor の外部では、ユーザーの要求からのファイル名の内容を常に <xref:System.Net.WebUtility.HtmlEncode*> でエンコードします。

多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。 アプリの仕様を満たす追加のロジックを提供します。

### <a name="size-validation"></a>サイズの検証

アップロードされるファイルのサイズを制限します。

サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。 その制限は、[appsettings.json](xref:fundamentals/configuration/index) ファイルの *Configuration* によって提供されます。

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` が `PageModel` クラスに挿入されます。

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

ファイルのサイズが制限を超えると、ファイルは拒否されます。

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>name 属性の値を POST メソッドのパラメーター名に一致させる

フォーム データを POST する、または JavaScript の `FormData` を直接使用する、Razor 以外のフォームでは、フォームの要素または `FormData` で指定されている名前が、コントローラーのアクションのパラメーターの name と一致している必要があります。

たとえば、行が次のように表示されているとします。

* `<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。

* `Upload` という名前の Razor Pages ページ ハンドラー メソッドの場合:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST コントローラー アクション メソッドの場合:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>サーバーとアプリの構成

### <a name="multipart-body-length-limit"></a>マルチパート本文の長さの制限

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。 この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。 既定値は 134,217,728 (128 MB) です。 制限をカスタマイズするには、<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> の設定 `Startup.ConfigureServices` を使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を設定するには、<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。

Razor Pages アプリでは、[ の ](xref:razor-pages/razor-pages-conventions)convention`Startup.ConfigureServices` を使用してフィルターを適用します。

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Razor Pages アプリまたは MVC アプリでは、ページ モデルまたはアクション メソッドにフィルターを適用します。

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel の最大要求本文サイズ

Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。 制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>MaxRequestBodySize[ を設定するには、](xref:fundamentals/servers/kestrel#maximum-request-body-size) を使用します。

Razor Pages アプリでは、[ の ](xref:razor-pages/razor-pages-conventions)convention`Startup.ConfigureServices` を使用してフィルターを適用します。

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Razor Pages アプリまたは MVC アプリでは、ページ ハンドラー クラスまたはアクション メソッドにフィルターを適用します。

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>その他の Kestrel の制限

Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。

* [クライアントの最大接続数](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [要求と応答のデータ レート](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS の内容の長さの制限

既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。 *web.config* ファイルで制限をカスタマイズします。

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

この設定は IIS にのみ適用されます。 Kestrel でホストする場合、既定ではこの動作は発生しません。 詳細については、「[要求制限 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。

ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。 詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>IIS サーバーに展開したときの "見つかりません" エラー

次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。

### <a name="connection-failure"></a>接続エラー

接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。 詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。 Kestrel クライアント接続の制限の調整も必要な場合があります。

### <a name="null-reference-exception-with-iformfile"></a>IFormFile での null 参照例外

コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `enctype` に値 `multipart/form-data` が指定されていることを確認します。 この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。 また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。

### <a name="stream-was-too-long"></a>ストリームが長すぎる

このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。 `MemoryStream` のサイズ制限は `int.MaxValue` です。 アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。

::: moniker-end


## <a name="additional-resources"></a>その他のリソース

* [Unrestricted File Upload (ファイルの無制限のアップロード)](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [Azure のセキュリティ: セキュリティフレーム: 入力の検証 |対策](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure クラウドの設計パターン: Valet キーパターン](/azure/architecture/patterns/valet-key)
