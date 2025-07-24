## 🔐 What is **RoleManager** in ASP.NET Core Identity?

### ✅ Definition:

`RoleManager<TRole>` is a **built-in class** in ASP.NET Core Identity used to:

> 🧠 **Create, delete, update, and manage roles** in your application.

---

## 🎯 Why Do We Use **RoleManager**?

Because you often want to **control access** based on **roles** like:

- 👨‍💼 `Admin`
    
- 👩‍💼 `Manager`
    
- 👨‍🎓 `Student`
    
- 👨‍🔧 `Employee`
    

`RoleManager` helps you manage these roles easily **in code**, instead of manually creating them in the database.

---

## 🔧 Common Use Cases:

|Task|Done By `RoleManager`?|Example|
|---|---|---|
|Create a new role|✅ Yes|`"Admin"`, `"User"`|
|Check if a role exists|✅ Yes|`RoleManager.RoleExistsAsync("Admin")`|
|Delete a role|✅ Yes|`RoleManager.DeleteAsync(role)`|
|Get all roles|✅ Yes|`RoleManager.Roles.ToList()`|

---

## 🧱 Example: Creating a Role Using RoleManager

```c#
public class RoleSeeder
{
    private readonly RoleManager<IdentityRole> _roleManager;

    public RoleSeeder(RoleManager<IdentityRole> roleManager)
    {
        _roleManager = roleManager;
    }

    public async Task SeedRolesAsync()
    {
        if (!await _roleManager.RoleExistsAsync("Admin"))
        {
            await _roleManager.CreateAsync(new IdentityRole("Admin"));
        }

        if (!await _roleManager.RoleExistsAsync("User"))
        {
            await _roleManager.CreateAsync(new IdentityRole("User"));
        }
    }
}
```

### 📦 How to Inject RoleManager

In your controller or service:

```c#
public class AccountController : Controller
{
    private readonly RoleManager<IdentityRole> _roleManager;

    public AccountController(RoleManager<IdentityRole> roleManager)
    {
        _roleManager = roleManager;
    }
}

```

✅ This works if you already have this in `Program.cs` or `Startup.cs`:

```c#
builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### 🧠 Summary

| Concept        | RoleManager Helps You                      |
| -------------- | ------------------------------------------ |
| Manage roles   | Create, delete, check roles                |
| Use with users | Combine with `UserManager` to assign roles |
| Built-in class | Part of ASP.NET Core Identity              |
| Use in code    | Avoid manual role creation in DB           |
## ✅ Commonly Used Methods in `RoleManager<IdentityRole>`

|Method Name|Purpose|
|---|---|

### 🔸 `CreateAsync()`

Creates a new role.


`await _roleManager.CreateAsync(new IdentityRole("Admin"));`

---

### 🔸 `RoleExistsAsync(string roleName)`

Checks if a role already exists.

`bool exists = await _roleManager.RoleExistsAsync("Admin");`

---

### 🔸 `FindByNameAsync(string roleName)`

Finds a role by its name.

`var role = await _roleManager.FindByNameAsync("Admin");`

---

### 🔸 `FindByIdAsync(string roleId)`

Finds a role using its ID.

`var role = await _roleManager.FindByIdAsync("a7f1...123");`

---

### 🔸 `UpdateAsync(IdentityRole role)`

Updates a role's properties (e.g., name).

`role.Name = "SuperAdmin"; await _roleManager.UpdateAsync(role);`

---

### 🔸 `DeleteAsync(IdentityRole role)`

Deletes a role from the database.

`await _roleManager.DeleteAsync(role);`

---

### 🔸 `GetRoleNameAsync(IdentityRole role)`

Returns the name of a given role.

`string name = await _roleManager.GetRoleNameAsync(role);`

---

### 🔸 `SetRoleNameAsync(IdentityRole role, string name)`

Changes the name of the role.


`await _roleManager.SetRoleNameAsync(role, "PowerUser");`

---

### 🔸 `Roles`

Gets all roles as a queryable list.

`var allRoles = _roleManager.Roles.ToList();`

---

## 🧠 Bonus: Combining with `UserManager`

To assign a role to a user:

`await _userManager.AddToRoleAsync(user, "Admin");`

To get roles of a user:

`var roles = await _userManager.GetRolesAsync(user);`

---

## ✅ Summary

| Method              | What it does           |
| ------------------- | ---------------------- |
| `CreateAsync()`     | Create a new role      |
| `RoleExistsAsync()` | Check if a role exists |
| `FindByNameAsync()` | Get role by name       |
| `DeleteAsync()`     | Remove a role          |
| `UpdateAsync()`     | Update role info       |
| `Roles`             | List all roles         |
### ✅ `IdentityRole` in ASP.NET Core Identity

`IdentityRole` is a class provided by **ASP.NET Core Identity** that represents a **user role** in your application — like `"Admin"`, `"User"`, `"Manager"`, etc.

#### 🧱 Defined in:

`Microsoft.AspNetCore.Identity.IdentityRole`

---

### ✅ What It Contains

An instance of `IdentityRole` includes properties like:

`public class IdentityRole {  
public string Id { get; set; }           // Unique role ID  
public string Name { get; set; }         // Role name (e.g., "Admin")  
public string NormalizedName { get; set; } // For case-insensitive comparisons }`

## Step 1
1)Create Administrative Controller

Controller->RightClick->AddController->Mvc Controller with read/write actions

2)Create Interface and Implementation like(IsecurityRepository)

```c#
using Microsoft.AspNetCore.Identity;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace StockSphere.Service.Repository.Interface
{
    public interface ISecurityRepository
    {
        public Task<List<IdentityRole>> GetAllRolesAsync();

        public Task<IdentityResult> CreateRoleAsync(IdentityRole role);

        public Task DeleteRoleAsync(IdentityRole role);

        public Task UpdateRoleAsync(IdentityRole role);
    }
}
```

3)In that Implementation we use Usermanager<>,Role Manager<>,so add in that construstor...These 2 Services have Separate Methods.

```c#
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;
using StockSphere.Entity.Security;
using StockSphere.Service.Repository.Interface;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security;
using System.Text;
using System.Threading.Tasks;

namespace StockSphere.Service.Repository.Implementation
{
    public class SecurityRepository : ISecurityRepository
    {
        private RoleManager<IdentityRole> _roleManager;

        private UserManager<ApplicationUser> _userManager;

        public SecurityRepository(RoleManager<IdentityRole> roleManager, UserManager<ApplicationUser> userManager)
        {
            _roleManager = roleManager;
            this._userManager = userManager;
        }
        public async Task<IdentityResult> CreateRoleAsync(IdentityRole role)
        {
            IdentityResult result = await _roleManager.CreateAsync(role);
            return result;

        }

        public async Task DeleteRoleAsync(IdentityRole role)
        {
            IdentityResult result = await _roleManager.DeleteAsync(role);
        }

        public async Task<List<IdentityRole>> GetAllRolesAsync()
        {
            return await _roleManager.Roles.ToListAsync();
        }

        public async Task UpdateRoleAsync(IdentityRole role)
        {
            throw new NotImplementedException();
        }
    }
}
```

## The Repository

```c#
public interface ISecurityRepository
    {
        public Task<List<IdentityRole>> GetAllRolesAsync();

        public Task CreateRoleAsync(IdentityRole role);

        public Task DeleteRoleAsync(IdentityRole role);

        public Task UpdateRoleAsync(IdentityRole role);
    }
}
```
    
What is IdentityRole?

`IdentityRole` is a built-in class provided by **ASP.NET Core Identity** that represents a **role** in the identity system — such as `Admin`, `User`, `Manager`, etc.

- It is defined in the `Microsoft.AspNetCore.Identity` namespace.
    
- Used to manage role-based **authorization**.
    
- Works with the `RoleManager<IdentityRole>` and `UserManager<ApplicationUser>` classes.


`IdentityRole` **is mapped** to the `AspNetRoles` table in your database.
