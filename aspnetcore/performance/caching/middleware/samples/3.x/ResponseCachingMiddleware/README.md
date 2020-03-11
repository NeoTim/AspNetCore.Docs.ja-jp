# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 応答キャッシュのサンプル

このサンプルでは、ASP.NET Core[応答キャッシュミドルウェア](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)の使用方法を示します。

アプリは、インデックスページを使用して応答します。これには、キャッシュ動作を構成するための `Cache-Control` ヘッダーも含まれます。 また、アプリは `Vary` ヘッダーを設定して、後続の要求の `Accept-Encoding` ヘッダーが元の要求と一致する場合にのみ、応答を提供するようにキャッシュを構成します。

サンプルを実行すると、インデックスページはキャッシュに格納され、最大10秒間キャッシュされます。

キャッシュの動作をテストするには:

* ブラウザーを使用してキャッシュ動作をテストしないでください。 多くの場合、ブラウザーは、ミドルウェアがキャッシュされたページを提供しないようにするために、再読み込み時にキャッシュ制御ヘッダーを追加します。 たとえば、値が `max-age=0`) の `Cache-Control` ヘッダーは、ブラウザーによって追加される場合があります。
* <a href="https://www.telerik.com/fiddler">Fiddler</a>や<a href="https://www.getpostman.com/">Postman</a>などの要求ヘッダーを明示的に設定できる開発者ツールを使用します。
