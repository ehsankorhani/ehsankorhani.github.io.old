---
layout: post
title: "JWT with ASP.NET Core – Users Data"
date: 2018-05-03
author: ehsan
categories: [Web Security]
excerpt_separator: <!--more-->
---

In the previous post, we implemented a middleware to authenticate and authorize user requests.
The token we receive from the user requests should include their identity, claims, and expiry date. but how do they get this token in the first place?
<!--more-->
**Database**

Obviously, there should be a persistence location to store. The database tables which hold user identity and claims could be in the following shape:


<img src="/assets/UserTable.jpg" alt="User table" />

<img src="/assets/UserClaimTable.jpg" alt="UserClaim table" />


**DbContext**

First, we need to define two classes to represent our tables:

```csharp
[Table("User")]
public partial class AppUser
{
    [Required]
    [Key]
    public Guid UserId { get; set; }

    [Required]
    [StringLength(255)]
    public string UserName { get; set; }

    [Required]
    [StringLength(255)]
    public string Password { get; set; }
}
```
and;

```csharp
[Table("UserClaim")]
public class AppUserClaim
{
    [Required]
    [Key]
    public Guid ClaimId { get; set; }

    [Required]
    public Guid UserId { get; set; }

    [Required]
    public string ClaimType { get; set; }

    [Required]
    public string ClaimValue { get; set; }
}
```

Then we have to create our custom DbContext to be able to access these tables using Entity Framework:

```csharp
public class AppContext : DbContext
{
    public DbSet<AppUserClaim> Claims { get; set; }
    public DbSet<AppUser> Users { get; set; }

    private const string connectionString = @"...";

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(connectionString);
    }
}
```

Finally, we create a model, which combines User and Claims, and will build our final token.

```csharp
public class AppUserAuth
{
    public AppUserAuth() : base()
    {
        UserName = "Not authorized";
        BearerToken = string.Empty;
    }

    public string UserName { get; set; }
    public string BearerToken { get; set; }
    public bool IsAuthenticated { get; set; }
    public bool CanAccessProducts { get; set; }
    public bool CanAddProduct { get; set; }
    public bool CanSaveProduct { get; set; }
    public bool CanAccessCategories { get; set; }
    public bool CanAddCategory { get; set; }
}
```

The next post will discuss the logic to log in the user and return token.
