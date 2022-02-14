# Menu Tips and Tricks


<video width="320" height="240" controls>
  <source src="./_videos/MenuConfiguration.mp4" type="video/mp4">
</video>

## Adding Sprints Item to the Main Menu
Open the `SHLOKERPMenuContributor` class in the `Menus` folder.

![alt text](\_images\MenuContributor.png '')

Add the following code to the end of the `ConfigureMainMenuAsync` method:

```c#
context.Menu.AddItem(new ApplicationMenuItem(SHLOKERPMenus.Sprint,l["Menu:Sprint"], "/Sprints/Sprint")));
```

Run the project, login to the application with the username `admin` and the password `1q2w3E*` and see the new menu item has been added to the main menu:

![alt text](\_images\MenuBar.png '')

When you click to the `Sprints` menu item under the `Sprint` parent, you are being redirected to the new empty `Sprints` Page.

## Grouping

```c#
context.Menu.AddItem(
    new ApplicationMenuItem(
        "SHLOKERP",
        l["Menu:Management"],
        icon: "fa fa-user"
        )
        .AddItem(
            new ApplicationMenuItem(
                SHLOKERPMenus.Sprint,
                 l["Menu:Sprint"], 
                 "/Sprints/Sprint"
        )
    )
);

```

## Adding permission is easy.

Just enclose the menu with `IsGrantedAsync` function with if condition like below, 

```C#
if (await context.IsGrantedAsync(SHLOKERPPermissions.Sprint.Default))
{
        context.Menu.AddItem(
        new ApplicationMenuItem(
            SHLOKERPMenus.Sprint,
             l["Menu:Sprint"],
              "/Sprints/Sprint"
        ));
}
```

## Adding Icon to Menu Items.
```C#
context.Menu.AddItem(
    new ApplicationMenuItem(
        "SHLOKERP",
        l["Menu:Management"],
        icon: "fa fa-user" // icon
        );
```

## Reorder / Add / Remove -  default module menus (Tenant, users roles)

```C#
var administration = context.Menu.GetAdministration();
```

It is a default menu group on startup project.

- `Reorder`:    
    ```C#
        administration.SetSubItemOrder(IdentityMenuNames.GroupName, 1); // second Parameter is the order
        administration.SetSubItemOrder(SettingManagementMenuNames.GroupName, 2);
    ```
- `Add`:
    ```C#
         administration.AddItem(
            new ApplicationMenuItem(SHLOKERPMenus.Employee, l["Menu:Employee"], "/Employees/Employee", icon: "fas fa-user")
        );
    ```
- `Remove`:
    ```C#
        administration.TryRemoveMenuItem(TenantManagementMenuNames.GroupName);
    ```
> whole code looks like this,

```C#
private async Task ConfigureMainMenuAsync(MenuConfigurationContext context)
{
    var administration = context.Menu.GetAdministration();
    var l = context.GetLocalizer<SHLOKERPResource>();

    context.Menu.Items.Insert(
        0,
        new ApplicationMenuItem(
            SHLOKERPMenus.Home,
            l["Menu:Home"],
            "~/",
            icon: "fas fa-home",
            order: 0
        )
    );

    
    if (await context.IsGrantedAsync(SHLOKERPPermissions.Employee.Default))
    {
        administration.AddItem(
            new ApplicationMenuItem(SHLOKERPMenus.Employee, l["Menu:Employee"], "/Employees/Employee", icon: "fas fa-user")
        );
    }
    if (await context.IsGrantedAsync(SHLOKERPPermissions.Designation.Default))
    {
        administration.AddItem(
            new ApplicationMenuItem(SHLOKERPMenus.Designation, l["Menu:Designation"], "/Designations/Designation", icon: "fas fa-network-wired")
        );
    }

    administration.SetSubItemOrder(IdentityMenuNames.GroupName, 1);
    administration.SetSubItemOrder(SettingManagementMenuNames.GroupName, 2);

    administration.TryRemoveMenuItem(TenantManagementMenuNames.GroupName);
}
```

> This Video will clearly explain about adding permissions in Menu

<video width="320" height="240" controls>
  <source src="./_videos/MenuConfigurationAddingPermissions.mp4" type="video/mp4">
</video>
