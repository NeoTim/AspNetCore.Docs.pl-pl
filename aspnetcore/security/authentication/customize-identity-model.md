---
title: Dostosowywanie modelu tożsamości w programie ASP.NET Core
author: ajcvickers
description: W tym artykule opisano, jak dostosować bazowy model danych Entity Framework Core dla tożsamości platformy ASP.NET Core.
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500480"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="bed0c-103">Dostosowywanie modelu tożsamości w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bed0c-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="bed0c-104">Przez [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="bed0c-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="bed0c-105">Tożsamość platformy ASP.NET Core zapewnia platformę do zarządzania i przechowywania kont użytkowników w aplikacji platformy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bed0c-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="bed0c-106">Tożsamość zostanie dodany do projektu po **indywidualne konta użytkowników** jest wybrany jako mechanizm uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="bed0c-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="bed0c-107">Domyślnie, Identity korzysta z Entity Framework (EF) podstawowego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="bed0c-108">W tym artykule opisano sposób dostosowywania modelu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bed0c-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="bed0c-109">Tożsamość i migracji programu EF Core</span><span class="sxs-lookup"><span data-stu-id="bed0c-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="bed0c-110">Przed zbadaniem modelu, warto zrozumieć, jak działa tożsamości za pomocą [migracji programu EF Core](/ef/core/managing-schemas/migrations/) do tworzenia i aktualizowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="bed0c-111">Na najwyższym poziomie proces jest:</span><span class="sxs-lookup"><span data-stu-id="bed0c-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="bed0c-112">Zdefiniuj lub zaktualizować [modelu danych w kodzie](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="bed0c-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="bed0c-113">Dodaj migrację do tłumaczenia w tym modelu na zmiany, które mogą być stosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="bed0c-114">Sprawdź, czy migracja poprawnie reprezentuje zamiaru.</span><span class="sxs-lookup"><span data-stu-id="bed0c-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="bed0c-115">Zastosowanie migracji można zaktualizować bazy danych, aby był zsynchronizowany z modelem.</span><span class="sxs-lookup"><span data-stu-id="bed0c-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="bed0c-116">Powtórz kroki od 1 do 4, aby dodatkowo dostosować model i synchronizację bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="bed0c-117">Dodaj i stosowanie migracje, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="bed0c-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="bed0c-118">**Konsola Menedżera pakietów** okna (PMC), jeśli za pomocą programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bed0c-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="bed0c-119">Aby uzyskać więcej informacji, zobacz [narzędzia EF Core PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="bed0c-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="bed0c-120">.NET Core interfejsu wiersza polecenia, jeśli przy użyciu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bed0c-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="bed0c-121">Aby uzyskać więcej informacji, zobacz [narzędzi wiersza polecenia programu EF Core .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bed0c-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="bed0c-122">Klikając **zastosować migracje** przycisk na stronę błędu, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="bed0c-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="bed0c-123">Platforma ASP.NET Core ma program obsługi strony błędów w czasie projektowania.</span><span class="sxs-lookup"><span data-stu-id="bed0c-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="bed0c-124">Program obsługi można zastosować migracji, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="bed0c-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="bed0c-125">Aplikacje produkcyjne zazwyczaj Generuj skrypty SQL z migracje i wdrażania zmian w bazie danych w ramach kontrolowanego aplikacji i wdrażania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="bed0c-126">Po utworzeniu nowej aplikacji przy użyciu tożsamości kroki 1 i 2 powyżej została już ukończona.</span><span class="sxs-lookup"><span data-stu-id="bed0c-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="bed0c-127">Oznacza to, że istnieje już w modelu danych początkowych i początkowej migracji został dodany do projektu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="bed0c-128">Początkowej migracji nadal musi zostać zastosowana do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="bed0c-129">Można zastosować początkowej migracji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="bed0c-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="bed0c-130">Uruchom `Update-Database` w konsoli zarządzania Pakietami.</span><span class="sxs-lookup"><span data-stu-id="bed0c-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="bed0c-131">Uruchom `dotnet ef database update` w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="bed0c-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="bed0c-132">Kliknij przycisk **zastosować migracje** przycisk na stronę błędu, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="bed0c-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="bed0c-133">Powtórz te czynności, jak zmiany zostały wprowadzone do modelu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="bed0c-134">Modelu tożsamości</span><span class="sxs-lookup"><span data-stu-id="bed0c-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="bed0c-135">Typy jednostek</span><span class="sxs-lookup"><span data-stu-id="bed0c-135">Entity types</span></span>

<span data-ttu-id="bed0c-136">Model tożsamości składa się z następujących typów jednostki.</span><span class="sxs-lookup"><span data-stu-id="bed0c-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="bed0c-137">Typ jednostki</span><span class="sxs-lookup"><span data-stu-id="bed0c-137">Entity type</span></span>|<span data-ttu-id="bed0c-138">Opis</span><span class="sxs-lookup"><span data-stu-id="bed0c-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="bed0c-139">Reprezentuje użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bed0c-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="bed0c-140">Reprezentuje rolę.</span><span class="sxs-lookup"><span data-stu-id="bed0c-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="bed0c-141">Reprezentuje oświadczenie, które posiada użytkownik.</span><span class="sxs-lookup"><span data-stu-id="bed0c-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="bed0c-142">Reprezentuje token uwierzytelniania dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bed0c-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="bed0c-143">Kojarzy użytkownika z logowaniem.</span><span class="sxs-lookup"><span data-stu-id="bed0c-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="bed0c-144">Reprezentuje oświadczenie, które otrzymuje dla wszystkich użytkowników w ramach roli.</span><span class="sxs-lookup"><span data-stu-id="bed0c-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="bed0c-145">Jednostka sprzężenia, która kojarzy użytkownikami i rolami.</span><span class="sxs-lookup"><span data-stu-id="bed0c-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="bed0c-146">Relacje typów jednostek</span><span class="sxs-lookup"><span data-stu-id="bed0c-146">Entity type relationships</span></span>

<span data-ttu-id="bed0c-147">[Typów jednostek](#entity-types) są powiązane ze sobą w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="bed0c-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="bed0c-148">Każdy `User` może mieć wiele `UserClaims`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="bed0c-149">Każdy `User` może mieć wiele `UserLogins`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="bed0c-150">Każdy `User` może mieć wiele `UserTokens`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="bed0c-151">Każdy `Role` może mieć wiele skojarzonych `RoleClaims`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="bed0c-152">Każdy `User` może mieć wiele skojarzonych `Roles`, a każdy `Role` może być skojarzony z wieloma `Users`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="bed0c-153">Jest to relacja wiele do wielu, wymagającego sprzężenie tabeli w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="bed0c-154">Tabela sprzężenia jest reprezentowany przez `UserRole` jednostki.</span><span class="sxs-lookup"><span data-stu-id="bed0c-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="bed0c-155">Domyślna konfiguracja modelu</span><span class="sxs-lookup"><span data-stu-id="bed0c-155">Default model configuration</span></span>

<span data-ttu-id="bed0c-156">Tożsamość definiuje wiele *klasy kontekst* , dziedziczą z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) do konfigurowania i używania tego modelu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="bed0c-157">Ta konfiguracja jest implementowana przy użyciu [EF Core kodu pierwszy Fluent API](/ef/core/modeling/) w [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) metody z klasy kontekstu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="bed0c-158">Domyślna konfiguracja polega:</span><span class="sxs-lookup"><span data-stu-id="bed0c-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="bed0c-159">Typy ogólne w modelu</span><span class="sxs-lookup"><span data-stu-id="bed0c-159">Model generic types</span></span>

<span data-ttu-id="bed0c-160">Tożsamość definiuje domyślny [środowiska uruchomieniowego języka wspólnego](/dotnet/standard/glossary#clr) typów (CLR) dla każdego z typów jednostek wymienionych powyżej.</span><span class="sxs-lookup"><span data-stu-id="bed0c-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="bed0c-161">Te typy są poprzedzone z *tożsamości*:</span><span class="sxs-lookup"><span data-stu-id="bed0c-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="bed0c-162">Zamiast bezpośrednio przy użyciu tych typów, typy może służyć jako klay bazowe dla typów własnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="bed0c-163">`DbContext` Klas zdefiniowanych przez tożsamości są ogólne, w taki sposób, że różne typy CLR może służyć do co najmniej jeden z typów jednostek w modelu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="bed0c-164">Te typy rodzajowe również zezwolić `User` typu klucza podstawowego (PK) dane mają być zmienione.</span><span class="sxs-lookup"><span data-stu-id="bed0c-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="bed0c-165">W przypadku używania tożsamości z obsługą dla ról, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> klasa powinna być używana.</span><span class="sxs-lookup"><span data-stu-id="bed0c-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="bed0c-166">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bed0c-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="bed0c-167">Istnieje również możliwość użycia tożsamości bez role (tylko oświadczenia), w którym to przypadku <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> należy używać klasy:</span><span class="sxs-lookup"><span data-stu-id="bed0c-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="bed0c-168">Dostosuj model</span><span class="sxs-lookup"><span data-stu-id="bed0c-168">Customize the model</span></span>

<span data-ttu-id="bed0c-169">Punkt początkowy dla dostosowania modelu ma pochodzić od typu odpowiedniego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="bed0c-170">Zobacz [modelu typów ogólnych](#model-generic-types) sekcji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="bed0c-171">Ten typ kontekstu zwyczajowo jest nazywany `ApplicationDbContext` i zostanie utworzony za pomocą szablonów platformy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bed0c-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="bed0c-172">Kontekst jest używany do konfigurowania modelu na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="bed0c-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="bed0c-173">Podanie jednostek i typy kluczy dla parametrów typu ogólnego.</span><span class="sxs-lookup"><span data-stu-id="bed0c-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="bed0c-174">Zastępowanie `OnModelCreating` do modyfikowania mapowanie tych typów.</span><span class="sxs-lookup"><span data-stu-id="bed0c-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="bed0c-175">Podczas zastępowania `OnModelCreating`, `base.OnModelCreating` powinny zostać wywołane najpierw; nadrzędnych konfiguracji powinna być wywoływana dalej.</span><span class="sxs-lookup"><span data-stu-id="bed0c-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="bed0c-176">EF Core ogólnie ma zasady ostatni jednej usługi wins do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="bed0c-177">Na przykład jeśli `ToTable` dla typu jednostki wywoływana jest metoda najpierw o nazwie jedna tabela a następnie ponownie później z inną nazwę tabeli, nazwę tabeli drugie wywołanie jest używane.</span><span class="sxs-lookup"><span data-stu-id="bed0c-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="bed0c-178">Danych niestandardowych użytkownika</span><span class="sxs-lookup"><span data-stu-id="bed0c-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="bed0c-179">[Danych niestandardowych użytkownika](xref:security/authentication/add-user-data) jest obsługiwana przez dziedziczenie z `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="bed0c-180">Zwyczajowy nazwę tego typu jest `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="bed0c-181">Użyj `ApplicationUser` typu jako argumentu ogólnego kontekstu:</span><span class="sxs-lookup"><span data-stu-id="bed0c-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="bed0c-182">Nie ma potrzeby zastąpić `OnModelCreating` w `ApplicationDbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="bed0c-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="bed0c-183">EF Core mapuje `CustomTag` właściwości przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="bed0c-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="bed0c-184">Jednak bazy danych musi zostać zaktualizowany, aby utworzyć nowy `CustomTag` kolumny.</span><span class="sxs-lookup"><span data-stu-id="bed0c-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="bed0c-185">Aby utworzyć kolumnę, Dodaj migrację, a następnie zaktualizować bazy danych, zgodnie z opisem w [tożsamości i programem EF Core migracje](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="bed0c-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="bed0c-186">Aktualizacja *Pages/Shared/_LoginPartial.cshtml* i Zastąp `IdentityUser` z `ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="bed0c-187">Aktualizacja *Areas/Identity/IdentityHostingStartup.cs* lub `Startup.ConfigureServices` i Zastąp `IdentityUser` z `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="bed0c-188">W programie ASP.NET Core 2.1 lub nowszej tożsamość jest dostarczany jako biblioteki klas Razor.</span><span class="sxs-lookup"><span data-stu-id="bed0c-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="bed0c-189">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="bed0c-190">W związku z tym, poprzedni kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="bed0c-191">Jeśli Generator szkieletu tożsamości zostało użyte do dodania tożsamości plików do projektu, Usuń wywołanie funkcji `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="bed0c-192">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="bed0c-192">For more information, see:</span></span>

* [<span data-ttu-id="bed0c-193">Tworzenie szkieletu tożsamości</span><span class="sxs-lookup"><span data-stu-id="bed0c-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="bed0c-194">Dodawanie, pobieranie i usuwanie danych niestandardowych użytkownika w tożsamości</span><span class="sxs-lookup"><span data-stu-id="bed0c-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="bed0c-195">Zmień typ klucza podstawowego</span><span class="sxs-lookup"><span data-stu-id="bed0c-195">Change the primary key type</span></span>

<span data-ttu-id="bed0c-196">Zmiana typu danych kolumny klucza podstawowego, po utworzeniu bazy danych jest problematyczne na wiele systemów baz danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="bed0c-197">Zmiana klucza produktu zazwyczaj polega na porzucenie i ponowne utworzenie tabeli.</span><span class="sxs-lookup"><span data-stu-id="bed0c-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="bed0c-198">W związku z tym typy kluczy powinny być określone w początkowej migracji, po utworzeniu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="bed0c-199">Wykonaj następujące kroki, aby zmienić typ klucza produktu:</span><span class="sxs-lookup"><span data-stu-id="bed0c-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="bed0c-200">Jeśli baza danych została utworzona przed zmianą klucza produktu, uruchom `Drop-Database` (PMC) lub `dotnet ef database drop` (.NET Core interfejsu wiersza polecenia) go usunąć.</span><span class="sxs-lookup"><span data-stu-id="bed0c-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="bed0c-201">Po potwierdzeniu operacji usuwania bazy danych, należy usunąć początkowej migracji za pomocą `Remove-Migration` (PMC) lub `dotnet ef migrations remove` (.NET Core interfejsu wiersza polecenia).</span><span class="sxs-lookup"><span data-stu-id="bed0c-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="bed0c-202">Aktualizacja `ApplicationDbContext` pochodzi od klasy <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="bed0c-203">Określ nowy typ klucza `TKey`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="bed0c-204">Na przykład, aby użyć `Guid` typ klucza:</span><span class="sxs-lookup"><span data-stu-id="bed0c-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="bed0c-205">W poprzednim kodzie klas ogólnych <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> i <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> muszą określać używać nowego typu klucza.</span><span class="sxs-lookup"><span data-stu-id="bed0c-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="bed0c-206">W poprzednim kodzie klas ogólnych <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> i <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> muszą określać używać nowego typu klucza.</span><span class="sxs-lookup"><span data-stu-id="bed0c-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="bed0c-207">`Startup.ConfigureServices` należy zaktualizować tak, aby za pomocą ogólnego konta użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bed0c-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="bed0c-208">Jeśli niestandardowa `ApplicationUser` klasa jest używana, aktualizować klasę, aby dziedziczyć `IdentityUser`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="bed0c-209">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bed0c-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="bed0c-210">Aktualizacja `ApplicationDbContext` można odwoływać się do niestandardowego `ApplicationUser` klasy:</span><span class="sxs-lookup"><span data-stu-id="bed0c-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="bed0c-211">Rejestrowanie klasy kontekstu bazy danych niestandardowych podczas dodawania usługi tożsamości w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="bed0c-212">Typ danych klucza podstawowego jest wnioskowany, analizując [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) obiektu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="bed0c-213">W programie ASP.NET Core 2.1 lub nowszej tożsamość jest dostarczany jako biblioteki klas Razor.</span><span class="sxs-lookup"><span data-stu-id="bed0c-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="bed0c-214">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="bed0c-215">W związku z tym, poprzedni kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="bed0c-216">Jeśli Generator szkieletu tożsamości zostało użyte do dodania tożsamości plików do projektu, Usuń wywołanie funkcji `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="bed0c-217">Typ danych klucza podstawowego jest wnioskowany, analizując [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) obiektu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="bed0c-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> Metoda przyjmuje `TKey` typ wskazuje typ danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="bed0c-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="bed0c-219">Jeśli niestandardowa `ApplicationRole` klasa jest używana, aktualizować klasę, aby dziedziczyć `IdentityRole<TKey>`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="bed0c-220">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bed0c-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="bed0c-221">Aktualizacja `ApplicationDbContext` można odwoływać się do niestandardowego `ApplicationRole` klasy.</span><span class="sxs-lookup"><span data-stu-id="bed0c-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="bed0c-222">Na przykład następującej klasy odwołuje się do niestandardowego `ApplicationUser` i niestandardowego `ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="bed0c-223">Rejestrowanie klasy kontekstu bazy danych niestandardowych podczas dodawania usługi tożsamości w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="bed0c-224">Typ danych klucza podstawowego jest wnioskowany, analizując [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) obiektu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="bed0c-225">W programie ASP.NET Core 2.1 lub nowszej tożsamość jest dostarczany jako biblioteki klas Razor.</span><span class="sxs-lookup"><span data-stu-id="bed0c-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="bed0c-226">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="bed0c-227">W związku z tym, poprzedni kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span><span class="sxs-lookup"><span data-stu-id="bed0c-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="bed0c-228">Jeśli Generator szkieletu tożsamości zostało użyte do dodania tożsamości plików do projektu, Usuń wywołanie funkcji `AddDefaultUI`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="bed0c-229">Rejestrowanie klasy kontekstu bazy danych niestandardowych podczas dodawania usługi tożsamości w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="bed0c-230">Typ danych klucza podstawowego jest wnioskowany, analizując [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) obiektu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="bed0c-231">Rejestrowanie klasy kontekstu bazy danych niestandardowych podczas dodawania usługi tożsamości w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="bed0c-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> Metoda przyjmuje `TKey` typ wskazuje typ danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="bed0c-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="bed0c-233">Dodawanie właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="bed0c-233">Add navigation properties</span></span>

<span data-ttu-id="bed0c-234">Zmiana konfiguracji modelu dla relacji może być trudniejsze niż wprowadzeniem innych zmian.</span><span class="sxs-lookup"><span data-stu-id="bed0c-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="bed0c-235">Należy uważać, aby zastąpić istniejące relacje, zamiast tworzyć nowy, dodatkowe relacje.</span><span class="sxs-lookup"><span data-stu-id="bed0c-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="bed0c-236">W szczególności zmienione relacja musi określać samą właściwość klucza obcego (klucz OBCY), jako istniejącą relację.</span><span class="sxs-lookup"><span data-stu-id="bed0c-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="bed0c-237">Na przykład relacji między `Users` i `UserClaims` jest domyślnie określone w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="bed0c-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="bed0c-238">Klucza Obcego dla tej relacji jest określony jako `UserClaim.UserId` właściwości.</span><span class="sxs-lookup"><span data-stu-id="bed0c-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="bed0c-239">`HasMany` i `WithOne` są wywoływane bez argumentów, aby utworzyć relacje bez właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="bed0c-240">Właściwość nawigacji, aby dodać `ApplicationUser` umożliwiająca skojarzone `UserClaims` przywoływanie od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bed0c-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="bed0c-241">`TKey` Dla `IdentityUserClaim<TKey>` jest typ określony dla klucza podstawowego użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bed0c-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="bed0c-242">W tym przypadku `TKey` jest `string` ponieważ są używane wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="bed0c-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="bed0c-243">Ma ona **nie** typu klucz podstawowy dla `UserClaim` typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="bed0c-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="bed0c-244">Teraz, gdy istnieje właściwość nawigacji, muszą być skonfigurowane w `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="bed0c-245">Należy zauważyć, że relacja jest skonfigurowany, dokładnie tak, jak został wcześniej, tylko z określonych w wywołaniu do właściwości nawigacji `HasMany`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="bed0c-246">Właściwości nawigacji istnieją tylko w modelu platformy EF, a nie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="bed0c-247">Ponieważ klucza Obcego relacji nie została zmieniona, tego rodzaju zmiany modelu nie wymaga bazy danych do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="bed0c-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="bed0c-248">Można to sprawdzić przez dodanie migracji po wprowadzeniu zmian.</span><span class="sxs-lookup"><span data-stu-id="bed0c-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="bed0c-249">`Up` i `Down` metody są puste.</span><span class="sxs-lookup"><span data-stu-id="bed0c-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="bed0c-250">Dodaj wszystkich właściwości nawigacji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bed0c-250">Add all User navigation properties</span></span>

<span data-ttu-id="bed0c-251">Przy użyciu powyższej sekcji jako wskazówkę, poniższy przykład umożliwia skonfigurowanie właściwości nawigacji jednokierunkowe dla wszystkich relacji na użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bed0c-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="bed0c-252">Dodawanie użytkownika i roli właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="bed0c-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="bed0c-253">Przy użyciu powyższej sekcji jako wskazówkę, poniższy przykład umożliwia skonfigurowanie właściwości nawigacji dla wszystkich relacji na użytkownika i roli:</span><span class="sxs-lookup"><span data-stu-id="bed0c-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="bed0c-254">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="bed0c-254">Notes:</span></span>

* <span data-ttu-id="bed0c-255">W tym przykładzie zawiera również `UserRole` dołączyć obiektu, który jest wymagany do nawigacji w relacji wiele do wielu użytkowników do ról.</span><span class="sxs-lookup"><span data-stu-id="bed0c-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="bed0c-256">Pamiętaj, aby zmienić typy właściwości nawigacji, aby uwzględniała `ApplicationXxx` typy są obecnie używane zamiast `IdentityXxx` typów.</span><span class="sxs-lookup"><span data-stu-id="bed0c-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="bed0c-257">Pamiętaj, aby używać `ApplicationXxx` w ogólnej `ApplicationContext` definicji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="bed0c-258">Dodaj wszystkie właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="bed0c-258">Add all navigation properties</span></span>

<span data-ttu-id="bed0c-259">Przy użyciu powyższej sekcji jako wskazówkę, poniższy przykład umożliwia skonfigurowanie właściwości nawigacji dla wszystkich relacji na wszystkich typów jednostek:</span><span class="sxs-lookup"><span data-stu-id="bed0c-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="bed0c-260">Użyj kluczy złożonych</span><span class="sxs-lookup"><span data-stu-id="bed0c-260">Use composite keys</span></span>

<span data-ttu-id="bed0c-261">Poprzednich sekcji pokazano, zmieniając typ użycia klucza w modelu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bed0c-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="bed0c-262">Zmiana modelu klucza tożsamości do użycia kluczy złożonych nie jest obsługiwany lub zalecane.</span><span class="sxs-lookup"><span data-stu-id="bed0c-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="bed0c-263">Przy użyciu klucza złożonego za pomocą tożsamości polega na zmianę, jak tożsamość Menedżer kod współdziała z modelu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="bed0c-264">To dostosowanie wykracza poza zakres tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="bed0c-265">Zmienianie nazwy tabeli z kolumnami i zestawy reguł</span><span class="sxs-lookup"><span data-stu-id="bed0c-265">Change table/column names and facets</span></span>

<span data-ttu-id="bed0c-266">Aby zmienić nazwy tabel i kolumn, należy wywołać `base.OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="bed0c-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="bed0c-267">Następnie należy dodać konfigurację, aby przesłonić wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="bed0c-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="bed0c-268">Na przykład, aby zmienić nazwę w tabelach tożsamości:</span><span class="sxs-lookup"><span data-stu-id="bed0c-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="bed0c-269">Te przykłady korzystają z typów tożsamości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="bed0c-269">These examples use the default Identity types.</span></span> <span data-ttu-id="bed0c-270">Jeśli przy użyciu typu aplikacji, takich jak `ApplicationUser`, konfigurowanie tego typu, zamiast domyślnego typu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="bed0c-271">Poniższy przykład umożliwia zmianę niektórych nazw kolumn:</span><span class="sxs-lookup"><span data-stu-id="bed0c-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="bed0c-272">Niektóre typy kolumny bazy danych można skonfigurować z niektórymi *aspektami* (na przykład maksymalna `string` dozwoloną długość).</span><span class="sxs-lookup"><span data-stu-id="bed0c-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="bed0c-273">W poniższym przykładzie ustawiono maksymalną długość kolumny dla kilku `string` właściwości w modelu:</span><span class="sxs-lookup"><span data-stu-id="bed0c-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="bed0c-274">Mapowanie do innego schematu</span><span class="sxs-lookup"><span data-stu-id="bed0c-274">Map to a different schema</span></span>

<span data-ttu-id="bed0c-275">Schematy może zachowywać się inaczej w dostawcy baz danych.</span><span class="sxs-lookup"><span data-stu-id="bed0c-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="bed0c-276">Dla programu SQL Server, wartość domyślna jest utworzenie wszystkich tabel w *dbo* schematu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="bed0c-277">Tabele można tworzyć w różnych schematów.</span><span class="sxs-lookup"><span data-stu-id="bed0c-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="bed0c-278">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bed0c-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="bed0c-279">Ładowanie z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="bed0c-279">Lazy loading</span></span>

<span data-ttu-id="bed0c-280">W tej sekcji zostanie dodana jego obsługa dla proxy ładowanych z opóźnieniem w modelu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bed0c-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="bed0c-281">Ładowanie leniwy jest przydatne, ponieważ umożliwia ono pomyślne właściwości nawigacji, aby można używać bez zapewnienia ich jest załadowany.</span><span class="sxs-lookup"><span data-stu-id="bed0c-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="bed0c-282">Typy jednostek można wprowadzić odpowiednie dla powolne ładowanie na kilka sposobów, zgodnie z opisem w [dokumentacji programu EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="bed0c-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="bed0c-283">Dla uproszczenia należy użyć serwerów proxy ładowanych z opóźnieniem, co wymaga:</span><span class="sxs-lookup"><span data-stu-id="bed0c-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="bed0c-284">Instalacja [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) pakietu.</span><span class="sxs-lookup"><span data-stu-id="bed0c-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="bed0c-285">Wywołanie <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> wewnątrz [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="bed0c-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="bed0c-286">Publiczna typów z `public virtual` właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="bed0c-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="bed0c-287">W poniższym przykładzie pokazano wywołanie `UseLazyLoadingProxies` w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bed0c-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="bed0c-288">Zapoznaj się z powyższych przykładach, aby uzyskać wskazówki na temat dodawania właściwości nawigacji do typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="bed0c-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bed0c-289">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bed0c-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
