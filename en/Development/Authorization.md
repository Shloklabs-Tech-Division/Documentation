# Permissions & Authorizations
ABP Framework provides an `authorization` system based on the `ASP.NET Core's authorization infrastructure`. One major feature added on top of the standard authorization infrastructure is the **permission system** which allows to define permissions and enable/disable per role, user or client.

<video width="320" height="240" controls>
  <source src="./_videos/Authorizations&Permissions.mp4" type="video/mp4">
</video>

## Step 1 : Permission Names
A permission must have a unique name (a `string`). The best way is to define it as a `const`, so we can reuse the permission name.

Open the `SHLOKERPPermissions` class inside the `SHLOKERPP.Application.Contracts` project (in the `Permissions` folder).

![Alt text](\_images\SHLOKERPPermissions.png)

Change the content as shown below:

```c#
namespace SHLOKERP.Permissions
{
    public static class SHLOKERPPermissions
    {
        public const string GroupName = "SHLOKERP";

        //Add your own permission names. Example:
        //public const string MyPermission1 = GroupName + ".MyPermission1";

        public class Sprint
        {
            public const string Default = GroupName + ".Sprint";
            public const string Update = Default + ".Update";
            public const string Create = Default + ".Create";
            public const string Delete = Default + ".Delete";
        }
    }
}
```
This is a hierarchical way of defining permission names. For example, "create sprint" permission name was defined as `SHLOKERP.Sprints.Create`. ABP doesn't force you to a structure, but we find this way useful.

## Step 2 : Permission Definitions
You should define permissions before using them.

Open the `SHLOKERPPermissionDefinitionProvider` class inside the `SHLOKERP.Application.Contracts` project (in the Permissions folder).

![Alt text](\_images\SHLOKERPPermissionDefinitionProvider.png)

 Change the content as shown below:

 ```c#
using SHLOKERP.Localization;
using Volo.Abp.Authorization.Permissions;
using Volo.Abp.Localization;

namespace SHLOKERP.Permissions
{
    public class SHLOKERPPermissionDefinitionProvider : PermissionDefinitionProvider
    {
        public override void Define(IPermissionDefinitionContext context)
        {
            var myGroup = context.AddGroup(SHLOKERPPermissions.GroupName);

            //Define your own permissions here. Example:
            //myGroup.AddPermission(SHLOKERPPermissions.MyPermission1, L("Permission:MyPermission1"));

            var sprintPermission = myGroup.AddPermission(SHLOKERPPermissions.Sprint.Default, L("Permission:Sprint"));
            sprintPermission.AddChild(SHLOKERPPermissions.Sprint.Create, L("Permission:Create"));
            sprintPermission.AddChild(SHLOKERPPermissions.Sprint.Update, L("Permission:Update"));
            sprintPermission.AddChild(SHLOKERPPermissions.Sprint.Delete, L("Permission:Delete"));
        }

        private static LocalizableString L(string name)
        {
            return LocalizableString.Create<SHLOKERPResource>(name);
        }
    }
}

 ```
This class defines a **permission group** (to group permissions on the UI, will be seen below) and **4 permissions** inside this group. Also, **Create, Edit and Delete** are children of the `SHLOKERPPermissions.Sprints.Default` permission. A child permission can be selected **only if the parent was selected**.

## Step 3 : Adding Translations 

![alt text](\_images\Localization.png 'Localization')

Finally, edit the localization file (`en.json` under the `Localization/SHLOKERP` folder of the `SHLOKERP.Domain.Shared` project) to define the localization keys used above:

```c#
    "Permission:Sprint": "Sprint",
    "Permission:Create": "Create",
    "Permission:Update": "Update",
    "Permission:Delete": "Delete",
```

>Localization key names are arbitrary and there is no forcing rule. But we prefer the convention used above.

## Step 4 : Permission Management UI
Once you define the permissions, you can see them on the **permission management modal**.

Go to the *Administration -> Identity -> Roles* page, select Permissions action for the admin role to open the permission management modal:

![Alt text](\_images\PermissionsUI.png)

Grant the permissions you want and save the modal.

>Tip: New permissions are automatically granted to the admin role if you run the `SHLOKERP.DbMigrator` application.

## Step 5 : Application Layer & HTTP API

![Alt text](\_images\SprintAppService.png)

Open the `SprintAppService` class and add set the policy names as the permission names defined above:

```c#
using Microsoft.AspNetCore.Authorization;
using SHLOKERP.Permissions;
using SHLOKERP.Sprints.Dtos;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;
using Volo.Abp.Domain.Repositories;

namespace SHLOKERP.Sprints
{
    public class SprintAppService : CrudAppService
        <Sprint, SprintDto, Guid, PagedAndSortedResultRequestDto, CreateUpdateSprintDto, CreateUpdateSprintDto>,
        ISprintAppService
    {
        public SprintAppService(IRepository<Sprint, Guid> repository) : base(repository)
        {
            GetPolicyName = SHLOKERPPermissions.Sprint.Default;
            GetListPolicyName = SHLOKERPPermissions.Sprint.Default;
            CreatePolicyName = SHLOKERPPermissions.Sprint.Create;
            UpdatePolicyName = SHLOKERPPermissions.Sprint.Update;
            DeletePolicyName = SHLOKERPPermissions.Sprint.Delete;
        }
    }
}
```

Added code to the constructor. Base `CrudAppService` automatically uses these permissions on the CRUD operations. This makes the **application service** secure, but also makes the HTTP API secure since this service is automatically used as an **HTTP API** as explained before (see `auto API controllers`).

## Step 6 : Permission inclusion in Razor Page
While securing the HTTP API & the application service prevents unauthorized users to use the services, they can still navigate to the SHLOKERP page. While they will get authorization exception when the page makes the first AJAX call to the server, we should also authorize the page for a better user experience and security.

Open the `SHLOKERPWebModule`.

![Alt text](\_images\SHLOKERPWebModule.png)

Add the following code block inside the `ConfigureServices` method:

```c#
Configure<RazorPagesOptions>(options =>
{
    options.Conventions.AuthorizePage("/Sprints/Index", SHLOKERPPermissions.Sprints.Default);
    options.Conventions.AuthorizePage("/Sprints/CreateModal", SHLOKERPPermissions.Sprints.Create);
    options.Conventions.AuthorizePage("/Sprints/EditModal", SHLOKERPPermissions.Sprints.Edit);
});
```
## Step 7 : Hide the New Sprint Button
The SHLOKERP page has a Create Sprint button that should be invisible if the current user has no *Sprint Creation* permission.

![Alt text](\_images\CreateSprintButton.png)

Open the Pages/Sprints/Index.cshtml file and change the content as shown below:
 
 ```HTML
@page
@using SHLOKERP.Permissions
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Mvc.Localization
@using Volo.Abp.AspNetCore.Mvc.UI.Layout
@using SHLOKERP.Web.Pages.Sprints.Sprint
@using SHLOKERP.Localization
@using SHLOKERP.Web.Menus
@model IndexModel
@inject IPageLayout PageLayout
@inject IHtmlLocalizer<SHLOKERPResource> L
@inject IAuthorizationService Authorization
@{
    PageLayout.Content.Title = L["Sprint"].Value;
    PageLayout.Content.BreadCrumb.Add(L["Menu:Sprint"].Value);
    PageLayout.Content.MenuItemName = SHLOKERPMenus.Sprint;
}

@section scripts
{
    <abp-script src="/Pages/Sprints/Sprint/index.js" />
}
@section styles
{
    <abp-style src="/Pages/Sprints/Sprint/index.css"/>
}

<abp-card>
    <abp-card-header>
        <abp-row>
            <abp-column size-md="_6">
                <abp-card-title>@L["Sprint"]</abp-card-title>
            </abp-column>
            <abp-column size-md="_6" class="text-right">
			    @if (await Authorization.IsGrantedAsync(SHLOKERPPermissions.Sprint.Create))
                {
                <abp-button id="NewSprintButton"
                            text="@L["CreateSprint"].Value"
                            icon="plus"
                            button-type="Primary" />
                }
            </abp-column>
        </abp-row>
    </abp-card-header>
    <abp-card-body>
        <abp-table striped-rows="true" id="SprintTable" class="nowrap"/>
    </abp-card-body>
</abp-card>

 ```

* Added `@inject IAuthorizationService AuthorizationService` to access to the authorization service.
* Used `@if (await AuthorizationService.IsGrantedAsync(SHLOKERPPermissions.Sprints.Create))` to check the sprint creation permission to conditionally render the *Create Sprint* button.

## JavaScript Side <!-- {docsify-ignore} -->
Sprints table in the sprint management page has an actions button for each row. The actions button includes *Edit* and *Delete* actions:

![Alt text](\_images\EditDeleteActionButton.png)

We should hide an action if the current user has not granted for the related permission. Datatables row actions has a `visible` option that can be set to `false` to hide the action item.

Open the `Pages/Sprints/Index.js` inside the `SHLOKERP.Web` project.

![alt text](\_images\IndexJS.png '')

Add a `visible` option to the `Edit` action as shown below:

```Java Script
 text: l('Edit'),
    visible: abp.auth.isGranted('SHLOKERP.Sprint.Update'),
    action: function (data) {
        editModal.open({ id: data.record.id });
    }
```
Do same for the `Delete` action:

```Java Script
text: l('Delete'),
    visible: abp.auth.isGranted('SHLOKERP.Sprint.Delete'),
        confirmMessage: function (data) {
            return l('SprintDeletionConfirmationMessage', data.record.id);
        },
action: function (data) {
    service.delete(data.record.id)
        .then(function () {
            abp.notify.info(l('SuccessfullyDeleted'));
            dataTable.ajax.reload();
        });
}
```
* `abp.auth.isGranted(...)` is used to check a permission that is defined before.
* `visible` could also be get a function that returns a `bool` if the value will be calculated later, based on some conditions.

## Step 8 : Menu Item
Even we have secured all the layers of the SHLOKERP page, it is still visible on the main menu of the application. We should hide the menu item if the current user has no permission.

![alt text](\_images\MenuContributor.png '')

Open the `SHLOKERPMenuContributor` class, find the code block below:


```Java Script
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
And replace this code block with the following:

```C#
context.Menu.AddItem(
    new ApplicationMenuItem(
        "SHLOKERP",
        l["Menu:Management"],
        icon: "fa fa-user"
);

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
You also need to add `async` keyword to the `ConfigureMenuAsync` method and re-arrange the return values. The final `SHLOKERPMenuContributor` class should be the following:

```c#
using System.Threading.Tasks;
using SHLOKERP.Permissions;
using SHLOKERP.Localization;
using SHLOKERP.MultiTenancy;
using Volo.Abp.Identity.Web.Navigation;
using Volo.Abp.SettingManagement.Web.Navigation;
using Volo.Abp.TenantManagement.Web.Navigation;
using Volo.Abp.UI.Navigation;

namespace SHLOKERP.Web.Menus
{
    public class SHLOKERPMenuContributor : IMenuContributor
    {
        public async Task ConfigureMenuAsync(MenuConfigurationContext context)
        {
            if (context.Menu.Name == StandardMenus.Main)
            {
                await ConfigureMainMenuAsync(context);
            }
        }

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

            context.Menu.AddItem(new ApplicationMenuItem("SHLOKERP",l["Menu:Management"],icon: "fa fa-user")
                .AddItem(new ApplicationMenuItem("SHLOKERP.Employees", l["Menu:Employees"],url: "/Employees"))
                .AddItem(new ApplicationMenuItem("SHLOKERP.Groups", l["Menu:Group"], url: "/Groups")));

            if (MultiTenancyConsts.IsEnabled)
            {
                administration.SetSubItemOrder(TenantManagementMenuNames.GroupName, 1);
            }
            else
            {
                administration.TryRemoveMenuItem(TenantManagementMenuNames.GroupName);
            }

            administration.SetSubItemOrder(IdentityMenuNames.GroupName, 2);
            administration.SetSubItemOrder(SettingManagementMenuNames.GroupName, 3);
            if (await context.IsGrantedAsync(SHLOKERPPermissions.Sprint.Default))
            {
                context.Menu.AddItem(
                    new ApplicationMenuItem(SHLOKERPMenus.Sprint, l["Menu:Sprint"], "/Sprints/Sprint")
                );
            }
        }
    }
}
```

































