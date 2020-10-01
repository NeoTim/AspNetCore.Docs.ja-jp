<span data-ttu-id="b3bc0-101">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="b3bc0-102">上記のコマンドにより次が追加されます。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-102">The preceding commands add:</span></span>

* <span data-ttu-id="b3bc0-103">[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="b3bc0-104">.NET Core CLI の Entity Framework Core ツール。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="b3bc0-105">EF Core SQLite プロバイダー。これにより、EF Core パッケージが依存関係としてインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="b3bc0-106">スキャフォールディングに必要なパッケージ: `Microsoft.VisualStudio.Web.CodeGeneration.Design` と `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="b3bc0-107">アプリが環境ごとにデータベースコンテキストを構成できるようにする複数の環境構成に関するガイダンスについては、「<xref:fundamentals/environments#environment-based-startup-class-and-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b3bc0-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]