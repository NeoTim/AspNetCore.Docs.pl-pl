<span data-ttu-id="eeedf-101">Wygenerowany kod bazy danych tożsamości wymaga [migracji Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="eeedf-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="eeedf-102">Utwórz migracji i aktualizują bazę danych.</span><span class="sxs-lookup"><span data-stu-id="eeedf-102">Create a migration and update the database.</span></span> <span data-ttu-id="eeedf-103">Na przykład uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeedf-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eeedf-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eeedf-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eeedf-105">W programie Visual Studio **Konsola Menedżera pakietów**:</span><span class="sxs-lookup"><span data-stu-id="eeedf-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eeedf-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eeedf-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="eeedf-107">Parametr name "CreateIdentitySchema" dla polecenia `Add-Migration` jest dowolny.</span><span class="sxs-lookup"><span data-stu-id="eeedf-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="eeedf-108">`"CreateIdentitySchema"` Opis migracji.</span><span class="sxs-lookup"><span data-stu-id="eeedf-108">`"CreateIdentitySchema"` describes the migration.</span></span>
