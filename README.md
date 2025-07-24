# Asp.net
03)Identity Db Context
`IdentityDbContext` is a special type of `DbContext` provided by Microsoft in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace. It comes pre-configured with all the necessary **DbSets** and **schema mappings** for Identity-related entities like:

|Table Name (Default)|Purpose|
|---|---|
|`AspNetUsers`|Stores user data|
|`AspNetRoles`|Stores roles (Admin, User, etc.)|
|`AspNetUserRoles`|User-role relationships|
|`AspNetUserClaims`|Claims associated with users|
|`AspNetUserLogins`|External login data (Google, FB)|
|`AspNetUserTokens`|Auth tokens|
|`AspNetRoleClaims`|Claims associated with roles|

---

### ✅ Why Use It?

1. **Built-in Identity Tables**  
    It automatically sets up the tables and relationships needed for user and role management.
    
2. **Custom User Models**  
    You can extend `IdentityUser` to add custom properties (e.g., `DateOfBirth`, `ProfilePicture`) and still use `IdentityDbContext<TUser>` to manage them.
    
3. **Simplifies Configuration**  
    It saves you from manually configuring all the Identity-related tables and relationships in your `DbContext`.
    
4. **Integration with ASP.NET Core Identity**  
    It works seamlessly with Identity services like login, registration, password hashing, role-based authorization, etc.
    

---

### 🧩 Example
```c#
public class ApplicationUser : IdentityUser

{

    public string FullName { get; set; }

}

public class ApplicationDbContext : IdentityDbContext

{

    public ApplicationDbContext(DbContextOptions options)

        : base(options)

    {

    }

    // You can add your own DbSets here

    public DbSet Products { get; set; }

}
```


## How To Use Identity Db Context

1)Inherit ApplicationDbContext from IdentityDbContext.
   now you can use the services of Identity Db Context.it contain all predefined Dbsets.

## Identity User
### What is `IdentityUser` in ASP.NET Core?

`IdentityUser` is a **built-in class** provided by ASP.NET Core Identity system. It represents a **user in your application**, including their login credentials and related info.

---

### ✅ Provided by:

Namespace:

`using Microsoft.AspNetCore.Identity;`

---

### 🧠 What does it include?

Here are some key properties of `IdentityUser`:

|Property|Description|
|---|---|
|`Id`|Unique identifier for the user (GUID or string)|
|`UserName`|Username used to log in|
|`Email`|Email address|
|`PasswordHash`|Hashed version of the user's password|
|`PhoneNumber`|User's phone number|
|`EmailConfirmed`|Whether the email is verified|
|`LockoutEnabled`|Can be locked out of the system|
|`AccessFailedCount`|Login attempts before lockout|

---

### ✍️ Example:
```c#
public class ApplicationUser : IdentityUser
{
    public string FullName { get; set; } // Your custom property
}
```

You **inherit from `IdentityUser`** when you want to extend the built-in user with your own fields (like `FullName`, `Gender`, etc.).

---

### 🔧 How is it used?

When setting up Identity in `Startup.cs` (or `Program.cs` in .NET 6+):


`services.AddIdentity<ApplicationUser, IdentityRole>()         .AddEntityFrameworkStores<ApplicationDbContext>();`

This tells ASP.NET:

> “Use `ApplicationUser` (based on `IdentityUser`) as the user model, and store everything in `ApplicationDbContext`.”

---

### 🛠️ Related classes:

- `IdentityRole`: Represents a role (e.g., "Admin", "User").
    
- `UserManager<TUser>`: API to manage users (create, delete, change password, etc.).
    
- `SignInManager<TUser>`: API for login, logout, authentication.
    

---
# **My Notes**

1)Create A folder Security give a name as your wish
2)Inside That Folder create a class Anything Like Apllication User
    
	  a)The Class Must want tO Inherit from **IdentityUser**.
     
     b)Identity User is the base class Thats Maps Asp.net Users Table.
     
     c)By Extending Your Custom Class Application User Your Telling EFCore...I Want    to use AspNetUsers table with some extra field.
     
     d)Change program.cs IdentityUser to Application User because we Inherit and add some extra fields.
     
 ```c#
 builder.Services.AddIdentity<ApplicationUser, IdentityRole>().AddEntityFrameworkStores<ApplicationDbContext>();
```

3)DbContext uses Application User

```c#
public class ApplicationDbContext : IdentityDbContext
```

### 🔍 Why use `IdentityDbContext<ApplicationUser>`?

ASP.NET Core Identity provides a base class called `IdentityDbContext<TUser>` where `TUser` is your user entity. By specifying `ApplicationUser`, you're telling Identity:

> "Hey, I have a custom user class with additional properties beyond the default `IdentityUser`. Use that instead."

### ✅ What does this enable?

- It allows you to **extend the user model** with custom fields like `FullName`, `DateOfBirth`, etc.
- It ensures that **Entity Framework** knows how to map your custom user class to the database.
- It integrates your user model with Identity features like login, registration, roles, claims, etc.

So When You Migrate So EF Core Creates The ASPNET USER TABLE.Include all Properties from Identity User, Plus your Custom Fields.
