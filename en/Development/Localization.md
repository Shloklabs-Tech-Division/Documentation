# Localization
ABP's localization system is seamlessly integrated to the `Microsoft.Extensions.Localization` package and compatible with the **Microsoft's localization documentation**. It adds some useful features and enhancements to make it easier to use in real life application scenarios.

<video width="320" height="240" controls>
  <source src="./_videos/Localization.mp4" type="video/mp4">
</video>

## step 0 : Installation

>This package is already installed by default with the startup template. So, most of the time, you don't need to install it manually.

`Volo.Abp.Localization` is the core package of the localization system. Install it to your project using the package manager console (PMC):

```PMC
Install-Package Volo.Abp.Localization
```

## step 1 : Add `AbpLocalizationModule` dependency to our module

Then you can add **AbpLocalizationModule** dependency to your module `SHLOKERPDomainSharedModule.cs` inside `SHLOKERP.Domain.Shared`:

![Alt text](\_images\AbpLocalizationModule.png)

```c#
using Volo.Abp.Modularity;
using Volo.Abp.Localization;
namespace SHLOKERP
{
    [DependsOn(
        typeof(AbpLocalizationModule)
        )]
    public class SHLOKERPDomainSharedModule : AbpModule
    {
    }
}
```
### step I : Creating A Localization Resource

Then it should be added using `AbpLocalizationOptions` as shown below:

```c#
public override void PreConfigureServices(ServiceConfigurationContext context)
{
    SHLOKERPGlobalFeatureConfigurator.Configure();
    SHLOKERPModuleExtensionConfigurator.Configure();
}

public override void ConfigureServices(ServiceConfigurationContext context)
{
    Configure<AbpVirtualFileSystemOptions>(options =>
    {
        options.FileSets.AddEmbedded<SHLOKERPDomainSharedModule>();
    });

    

    Configure<AbpExceptionLocalizationOptions>(options =>
    {
        options.MapCodeNamespace("SHLOKERP", typeof(SHLOKERPResource));
    });
}
```
### step II : Add Default Resource
`AbpLocalizationOptions.DefaultResourceType` can be set to a resource type, so it is used when the localization resource was not specified:

```c#
Configure<AbpLocalizationOptions>(options =>
    {
        options.Resources
            .Add<SHLOKERPResource>("en")
            .AddBaseTypes(typeof(AbpValidationResource))
            .AddVirtualJson("/Localization/SHLOKERP");

        options.DefaultResourceType = typeof(SHLOKERPResource);
    });
```
>The *application startup template* sets `DefaultResourceType` to the localization resource of the application.

The final code looks like : 
```c#
using SHLOKERP.Localization;
using Volo.Abp.AuditLogging;
using Volo.Abp.BackgroundJobs;
using Volo.Abp.FeatureManagement;
using Volo.Abp.Identity;
using Volo.Abp.IdentityServer;
using Volo.Abp.Localization;
using Volo.Abp.Localization.ExceptionHandling;
using Volo.Abp.Modularity;
using Volo.Abp.PermissionManagement;
using Volo.Abp.SettingManagement;
using Volo.Abp.TenantManagement;
using Volo.Abp.Validation.Localization;
using Volo.Abp.VirtualFileSystem;

namespace SHLOKERP
{
    [DependsOn(
        typeof(AbpAuditLoggingDomainSharedModule),
        typeof(AbpBackgroundJobsDomainSharedModule),
        typeof(AbpFeatureManagementDomainSharedModule),
        typeof(AbpIdentityDomainSharedModule),
        typeof(AbpIdentityServerDomainSharedModule),
        typeof(AbpPermissionManagementDomainSharedModule),
        typeof(AbpSettingManagementDomainSharedModule),
        typeof(AbpTenantManagementDomainSharedModule),
        typeof(AbpLocalizationModule)
        )
        ),
        ]
    public class SHLOKERPDomainSharedModule : AbpModule
    {
        public override void PreConfigureServices(ServiceConfigurationContext context)
        {
            SHLOKERPGlobalFeatureConfigurator.Configure();
            SHLOKERPModuleExtensionConfigurator.Configure();
        }

        public override void ConfigureServices(ServiceConfigurationContext context)
        {
            Configure<AbpVirtualFileSystemOptions>(options =>
            {
                options.FileSets.AddEmbedded<SHLOKERPDomainSharedModule>();
            });

            Configure<AbpLocalizationOptions>(options =>
            {
                options.Resources
                    .Add<SHLOKERPResource>("en")
                    .AddBaseTypes(typeof(AbpValidationResource))
                    .AddVirtualJson("/Localization/SHLOKERP");

                options.DefaultResourceType = typeof(SHLOKERPResource);
            });

            Configure<AbpExceptionLocalizationOptions>(options =>
            {
                options.MapCodeNamespace("SHLOKERP", typeof(SHLOKERPResource));
            });
        }
    }
}
```

## step 2 : Creating A Localization Resource
A localization resource is used to group related localization strings together and separate them from other localization strings of the application. 

A module generally defines its own localization resource. Localization resource is just a plain class. 

Create a class for `Localization Resource` inside `Localization` folder of project `SHLOKERP.Domain.Shared`

![Alt text](\_images\LocalizationResource.png)

## Short Localization Resource Name
Localization resources are also available in the client (JavaScript) side. So, setting a short name for the localization resource makes it easy to use localization texts.
```c#
    [LocalizationResourceName("SHLOKERP")]
    public class SHLOKERPResource
    {

    }

```

Add the following code: 
```c#
using Volo.Abp.Localization;

namespace SHLOKERP.Localization
{
    [LocalizationResourceName("SHLOKERP")]
    public class SHLOKERPResource
    {

    }
}
```

* Add a new localization resource with "en" (English) as the default culture.
* Used JSON files to store the localization strings.
* JSON files are embedded into the assembly using `AbpVirtualFileSystemOptions`.

JSON files are located under `/Localization/SHLOKERP` project folder as shown below:

![Alt text](\_images\Localization.png)

A JSON localization file content is shown below:

```JSON
{
  "culture": "en",
  "texts": {
    "Menu:Home": "Home",
    "Welcome": "Welcome",
    "LongWelcomeMessage": "Welcome to the application. This is a startup project based on the ABP framework. For more information, visit abp.io.",
    "Menu:Sprint": "Sprint",
    "Sprint": "Sprint",
    "SprintSprintName": "SprintName",
    "SprintWorkersCount": "WorkersCount",
    "SprintDuration": "Duration",
    "CreateSprint": "CreateSprint",
    "EditSprint": "EditSprint",
    "SprintDeletionConfirmationMessage": "Are you sure to delete the sprint {0}?",
    "Menu:Employees": "Employees",
    "Actions": "Actions",
    "Employees": "Employees",
    "Close": "Close",
    "Delete": "Delete",
    "Edit": "Edit",
    "PublishDate": "Publish date",
    "NewEmployees": "New Employees",
    "Update": "Update",
    "EmployeeName": "Name",
    "EmployeeCode": "Type",
    "DateOfBirth": "Price",
    "Salary": "Creation time",
    "NewEmployee": "New Employee",
    "AreYouSure": "Are you sure?",
    "AreYouSureToDelete": "Are you sure you want to delete this item?",
    "EmployeeDeletionConfirmationMessage": "Are you sure to delete the Employee '{0}'?",
    "SuccessfullyDeleted": "Successfully deleted!",
    "Menu:Group": "Groups",
    "Groups": "Groups",
    "GroupName": "Name",
    "MembersCount": "Total Users",
    "GroupDeletionConfirmationMessage": "Are you sure to delete the Group '{0}'?",
    "Menu:Management": "Management",
    "Permission:Sprint": "Sprint",
    "Permission:Create": "Create",
    "Permission:Update": "Update",
    "Permission:Delete": "Delete",
    "Permission:Revenue": "Revenue",
    "Menu:Revenue": "Revenue",
    "Revenue": "Revenue",
    "RevenueProjectName": "RevenueProjectName",
    "RevenueIncome": "RevenueIncome",
    "CreateRevenue": "CreateRevenue",
    "EditRevenue": "EditRevenue",
    "RevenueDeletionConfirmationMessage": "Are you sure to delete the revenue {0}?"
  }
}
```

* Every localization file should define the `culture` code for the file (like "en" or "en-US").
* `texts` section just contains key-value collection of the localization strings (keys may have spaces too).

## Format Arguments
If your localized string contains arguments, like `Hello {0}, welcome!`, you can pass arguments to the localization methods. 

Examples:

```JavaScript
var testSource = abp.localization.getResource('Test');
var str1 = testSource('HelloWelcomeMessage', 'John');
var str2 = abp.localization.localize('HelloWelcomeMessage', 'Test', 'John');

```
Assuming the `HelloWelcomeMessage` is localized as `Hello {0}, welcome!`, both of the samples above produce the output `Hello John, welcome!`.

## Using In A Razor View/Page
Use `IHtmlLocalizer<T>` in razor views/pages;

- `Dependency Injection`: 
    ```c#
        @inject IHtmlLocalizer<SHLOKERPResource> L
    ```
-  `Add Localized Html String`
    ```c#
        <abp-modal-header title="@L["CreateSprint"].Value"></abp-modal-header>
    ```

### Example `CreateModal.cshtml` of `Sprint`

```HTML
@page
@using Microsoft.AspNetCore.Mvc.Localization
@using Volo.Abp.AspNetCore.Mvc.UI.Bootstrap.TagHelpers.Modal;
@using SHLOKERP.Localization
@inject IHtmlLocalizer<SHLOKERPResource> L
@model SHLOKERP.Web.Pages.Sprints.Sprint.CreateModalModel
@{
    Layout = null;
}
<abp-dynamic-form abp-model="ViewModel" data-ajaxForm="true" asp-page="CreateModal">
    <abp-modal>
        <abp-modal-header title="@L["CreateSprint"].Value"></abp-modal-header>
        <abp-modal-body>
            <abp-form-content />
        </abp-modal-body>
        <abp-modal-footer buttons="@(AbpModalButtons.Cancel|AbpModalButtons.Save)"></abp-modal-footer>
    </abp-modal>
</abp-dynamic-form>
```

## Special Base Classes
Some ABP Framework base classes provide a L property to use the localizer even easier.

Example: Localize a text in an application service method

![Alt text](\_images\SHLOKERPAppService.png)

The Class contains following code:

```c#
using SHLOKERP.Localization;
using Volo.Abp.Application.Services;

namespace SHLOKERP
{
    /* Inherit your application services from this class.*/
    public abstract class SHLOKERPAppService : ApplicationService
    {
        protected SHLOKERPAppService()
        {
            LocalizationResource = typeof(SHLOKERPResource);
        }
    }
}
```

When you set the `LocalizationResource` in the constructor, the `ApplicationService class` uses that resource type when you use the `L` property.

Setting `LocalizationResource` in every application service can be tedious. You can create an abstract base application service class, set it there and derive your application services from that base class. This is already implemented when you create a new project with the startup templates. So, you can simply inherit from the base class directly use the `L` property

Example `SHLOKERPPageModel.cs` of `SHLOKERP.Web.Pages` project:

![Alt text](\_images\SHLOKERPPageModel.png)

The Class contains following code:


```c#
using SHLOKERP.Localization;
using Volo.Abp.AspNetCore.Mvc.UI.RazorPages;

namespace SHLOKERP.Web.Pages
{
    /* Inherit your PageModel classes from this class.
     */
    public abstract class SHLOKERPPageModel : AbpPageModel
    {
        protected SHLOKERPPageModel()
        {
            LocalizationResourceType = typeof(SHLOKERPResource);
        }
    }
}
```

The `L` property is also available for some other base classes like `AbpController` and `AbpPageModel`.

# Client side
Localization API allows you to reuse the server side localization resources in the client side.

## Basic Usage

Consider the example `Index.js` of `Sprint` under the `Pages/Sprints` folder in `SHLOKERP.Web` Project:

![alt text](\_images\IndexJS.png '')

- `abp.localization.getResource(...)` function is used to get a localization resource:
    ```JavaScript
    var l = abp.localization.getResource('SHLOKERP');
    ```
- Then you can localize a string based on this resource:
    ```JavaScript
    text: l('Edit')
    ```
- `abp.localization.localize(...)` function is a shortcut where you can both specify the text name and the resource name:
    ```JavaScript
    text: abp.localization.localize(('Edit','SHLOKERP')
    ```
    **Edit** is the text to *localize*, where **SHLOKERP** is the *localization resource name* here.


The content of the file is shown below:
```JavaScript
$(function () {

    var l = abp.localization.getResource('SHLOKERP');

    var service = sHLOKERP.sprints.sprint;
    var createModal = new abp.ModalManager(abp.appPath + 'Sprints/Sprint/CreateModal');
    var editModal = new abp.ModalManager(abp.appPath + 'Sprints/Sprint/EditModal');

    var dataTable = $('#SprintTable').DataTable(abp.libs.datatables.normalizeConfiguration({
        processing: true,
        serverSide: true,
        paging: true,
        searching: false,
        autoWidth: false,
        scrollCollapse: true,
        order: [[0, "asc"]],
        ajax: abp.libs.datatables.createAjax(service.getList),
        columnDefs: [
            {
                rowAction: {
                    items:
                        [
                            {
                                text: l('Edit'),
                                visible: abp.auth.isGranted('SHLOKERP.Sprint.Update'),
                                action: function (data) {
                                    editModal.open({ id: data.record.id });
                                }
                            },
                            {
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
                            }
                        ]
                }
            },
            {
                title: l('SprintSprintName'),
                data: "sprintName"
            },
            {
                title: l('Revenue'),
                data: "revenueIncome"
            },
            {
                title: l('SprintWorkersCount'),
                data: "workersCount"
            },
            {
                title: l('SprintDuration'),
                data: "duration"
            },
        ]
    }));

    createModal.onResult(function () {
        dataTable.ajax.reload();
    });

    editModal.onResult(function () {
        dataTable.ajax.reload();
    });

    $('#NewSprintButton').click(function (e) {
        e.preventDefault();
        createModal.open();
    });
});
```

## Fallback Logic
If given texts was not localized, localization method returns the given key as the localization result.

## Default Localization Resource
If you don't specify the localization resource name, it uses the default localization resource defined on the `AbpLocalizationOptions`.

Example: Using the default localization resource

```JavaScript
    text: abp.localization.localize(('Edit') //uses the default resource
```
# Other Properties & Methods <!-- {docsify-ignore} -->
## abp.localization.values
`abp.localization.values` property stores all the localization resources, keys and their values.

## abp.localization.isLocalized
Returns a boolean indicating that if the given text was localized or not.

Example
```JavaScript
abp.localization.isLocalized('ProductName', 'MyResource');
```
Returns `true` if the `ProductName` text was localized for the `MyResource` resource. Otherwise, returns `false`. You can leave the resource name empty to use the default localization resource.

## abp.localization.defaultResourceName
`abp.localization.defaultResourceName` can be set to change the default localization resource. You normally don't set this since the ABP Framework automatically sets is based on the server side configuration.

## abp.localization.currentCulture
`abp.localization.currentCulture` returns an object to get information about the **currently selected language**.

An example value of this object is shown below:
```JavaScript
{
  "displayName": "English",
  "englishName": "English",
  "threeLetterIsoLanguageName": "eng",
  "twoLetterIsoLanguageName": "en",
  "isRightToLeft": false,
  "cultureName": "en",
  "name": "en",
  "nativeName": "English",
  "dateTimeFormat": {
    "calendarAlgorithmType": "SolarCalendar",
    "dateTimeFormatLong": "dddd, MMMM d, yyyy",
    "shortDatePattern": "M/d/yyyy",
    "fullDateTimePattern": "dddd, MMMM d, yyyy h:mm:ss tt",
    "dateSeparator": "/",
    "shortTimePattern": "h:mm tt",
    "longTimePattern": "h:mm:ss tt"
  }
}
```

## abp.localization.languages
Used to get list of all **available languages** in the application. An example value of this object is shown below:

```JavaScript
abp.localization.isLocalized('ProductName', [
  {
    "cultureName": "en",
    "uiCultureName": "en",
    "displayName": "English",
    "flagIcon": null
  },
  {
    "cultureName": "fr",
    "uiCultureName": "fr",
    "displayName": "Français",
    "flagIcon": null
  },
  {
    "cultureName": "pt-BR",
    "uiCultureName": "pt-BR",
    "displayName": "Português",
    "flagIcon": null
  },
  {
    "cultureName": "tr",
    "uiCultureName": "tr",
    "displayName": "Türkçe",
    "flagIcon": null
  },
  {
    "cultureName": "zh-Hans",
    "uiCultureName": "zh-Hans",
    "displayName": "简体中文",
    "flagIcon": null
  }
]
```