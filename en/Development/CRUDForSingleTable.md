>In this section, you will create an application service to get, create, update and delete sprints using the `CrudAppService` base class of the ABP Framework.

<video width="320" height="240" controls>
  <source src="./_videos/SingleTableCrud.mp4" type="video/mp4">
</video>

## step 1 : Create the Domain Entity 

Domain layer in the startup template is separated into two projects:

* `SHLOKERP.Domain` contains your entities, domain services and other core domain objects.
* `SHLOKERP.Domain.Shared` contains constants, enums or other domain related objects those can be shared with clients.

So, define your entities in the domain layer (`SHLOKERP.Domain` project) of the solution. 

The main entity of the application is the Sprint. Create a Sprints folder (namespace) in the `SHLOKERP.Domain` project .

![Alt text](\_images\Sprint.png)

Add a Sprint class inside it:

<pre>
 <code>
using System;
using Volo.Abp.Domain.Entities.Auditing;

namespace SHLOKERP.Sprints
{
    public class Sprint : AuditedAggregateRoot<Guid>
    {
        public virtual string SprintName { get; set; }
        public virtual int WorkersCount { get; set; }
        <span class="highlighted-line">public virtual int Duration { get; set; } </span>
    }
}
 </code>
</pre>
* ABP Framework has two fundamental base classes for entities: `AggregateRoot` and `Entity`. Aggregate Root is a *Domain Driven Design* concept which can be thought as a root entity that is directly queried and worked on.
* `Sprint` entity inherits from the `AuditedAggregateRoot` which adds some base auditing properties (like `CreationTime, CreatorId, LastModificationTime...`) on top of the `AggregateRoot` class. ABP automatically manages these properties for you.
* `Guid` is the primary key type of the `Sprint` entity.

## step 2 : Add Sprint Entity to the DbContext
EF Core requires to relate entities with your `DbContext`.

![Alt text](\_images\DBContext.png)

 The easiest way to do this is to add a `DbSet` property to the `SHLOKERPDbContext` class in the `SHLOKERP.EntityFrameworkCore` project, as shown below:
```c#
[ConnectionStringName("Default")]
    public class SHLOKERPDbContext : AbpDbContext<SHLOKERPDbContext>
    {
        public DbSet<Sprint> Sprints { get; set; }    
    }
}
```
## step 3 : Map the Sprint Entity to a Database Table
Open `SHLOKERPDbContextModelCreatingExtensions.cs` file in the `SHLOKERP.EntityFrameworkCore` project and add the mapping code for the Sprint entity. 

![Alt text](\_images\DBContextModelCreateExtsn.png)

The final class should be the following:

```c#
using SHLOKERP.Sprints;
using Microsoft.EntityFrameworkCore;
using Volo.Abp;
using Volo.Abp.EntityFrameworkCore.Modeling;

namespace SHLOKERP.EntityFrameworkCore
{
    public static class SHLOKERPDbContextModelCreatingExtensions
    {
        public static void ConfigureSHLOKERP(this ModelBuilder builder)
        {
            Check.NotNull(builder, nameof(builder));

            builder.Entity<Sprint>(b =>
            {
                b.ToTable(SHLOKERPConsts.DbTablePrefix + "Sprints", SHLOKERPConsts.DbSchema);
                b.ConfigureByConvention();

                /* Configure more properties here */
                
            });
        }
    }
}
```
`SHLOKERPConsts` has constant values for schema and table prefixes for your tables. You don't have to use it, but suggested to control the table prefixes in a single point.

`ConfigureByConvention()` method gracefully configures/maps the inherited properties. Always use it for all your entities.

## step 4 : Add Database Migration
The startup solution is configured to use Entity Framework Core Code First Migrations. Since we've changed the database mapping configuration, we should create a new migration and apply changes to the database.

- Open the directory of the `SHLOKERP.EntityFrameworkCore.DbMigrations` project :
    ![alt text](\_images\DBmigrationsFolder.png 'DBmigrations')

-  Open a command-line terminal in the directory of the `SHLOKERP.EntityFrameworkCore.DbMigrations` project  :
    ![alt text](\_images\DBmigrationsFolderCMD.png 'DBmigrations')

- A terminal will be opened for the directory ,
Type the following command:
    ```Bash
        dotnet ef migrations add Added_Sprint_Entity
    ```
    Press `Enter`

- On Successfull Migration, The Terminal will display similar results
    ![alt text](\_images\DBmigrationsFolderCMD.png 'DBmigrations')

- This will add a new migration class to the project:

    ![alt text](\_images\DBmigrationsTerminal.png 'DBmigrations')

## step 5 : Update the Database

Run the `SHLOKERP.DbMigrator` application to update the database:
- Right Click the `SHLOKERP.DbMigrator` Project in Solution Explorer.

    ![alt text](\_images\DBmigratorProject.png 'UpdateDatabaseMigration')
- Which opens following options

    ![alt text](\_images\DBmigratorProjectDebug.png 'UpdateDatabaseMigration')
- Select `Debug` and Click `Start New Instance` which will open a terminal that automatically updates the database.

    ![alt text](\_images\DBmigratorProjectTerminal.png 'UpdateDatabaseMigration')

`.DbMigrator` is a console application that can be run to migrate the database schema and seed the data on development and production environments.

Add Some Sample Data

## step 6 : Create the Application Service
The application layer is separated into two projects:
* `SHLOKERP.Application.Contracts` contains your DTOs and application service interfaces.
* `SHLOKERP.Application` contains the implementations of your application services.


## step 7 : Creating the DTO Entity

> `Dto` - is called `Data Transfer Object`.

`CrudAppService` base class requires to define the fundamental DTOs for the entity. Create a Sprints folder (namespace) in the `SHLOKERP.Application.Contracts` project.

![Alt text](\_images\SprintDTO.png)

Add a `SprintDto` class inside it:

```c#
using System;
using Volo.Abp.Application.Dtos;

namespace SHLOKERP.Sprints.Dtos
{
    [Serializable]
    public class SprintDto : AuditedEntityDto<Guid>
    {
        public string SprintName { get; set; }

        public int WorkersCount { get; set; }

        public int Duration { get; set; }

    }
}
```

* DTO classes are used to transfer data between the presentation layer and the application layer.
* `SprintDto` is used to transfer sprint data to the presentation layer in order to show the sprint information on the UI.
* `SprintDto` is derived from the `AuditedEntityDto<Guid`> which has audit properties just like the `Sprint` entity defined above.

## step 8 : Map the DTO Entity with Domain Entity

It will be needed to map Sprint entities to `SprintDto` objects while returning `Sprints` to the presentation layer. AutoMapper library can automate this conversion when you define the proper mapping. The startup template comes with AutoMapper pre-configured. 

![Alt text](\_images\AutoMapperProfile.png)

So, you can just define the mapping in the `SHLOKERPApplicationAutoMapperProfile` class in the `SHLOKERP.Application` project:

```c#
using AutoMapper;
using SHLOKERP.Sprints;
using SHLOKERP.Sprints.Dtos;

namespace SHLOKERP
{
    public class SHLOKERPApplicationAutoMapperProfile : Profile
    {
        public SHLOKERPApplicationAutoMapperProfile()
        {
            /* You can configure your AutoMapper mapping configuration here.
             * Alternatively, you can split your mapping configurations
             * into multiple profile classes for a better organization. */

            CreateMap<Sprint, SprintDto>();
        }
    }
}
```

## step 9 : CreateUpdateSprintDto
Create a `CreateUpdateSprintDto` class in the Sprints folder (namespace) of the `SHLOKERP.Application.Contracts` project:

![Alt text](\_images\CreateUpdateSprintDTO.png)

Add the Code as mentioned below:


```c#
using System;
using System.ComponentModel;
namespace SHLOKERP.Sprints.Dtos
{
    [Serializable]
    public class CreateUpdateSprintDto
    {
        public string SprintName { get; set; }

        public int WorkersCount { get; set; }

        public int Duration { get; set; }
    }
}
```
* This `DTO` class is used to get sprint information from the user interface while creating or updating a sprint.
* It defines data annotation attributes (like `[Required]`) to define validations for the properties. DTOs are automatically validated by the ABP framework.

Just like done for the `SprintDto` above, we should define the mapping from the `CreateUpdateSprintDto` object to the Sprint entity. The final class will be like shown below:

```c#
using AutoMapper;
using SHLOKERP.Sprints;
using SHLOKERP.Sprints.Dtos;

namespace SHLOKERP
{
    public class SHLOKERPApplicationAutoMapperProfile : Profile
    {
        public SHLOKERPApplicationAutoMapperProfile()
        {
            /* You can configure your AutoMapper mapping configuration here.
             * Alternatively, you can split your mapping configurations
             * into multiple profile classes for a better organization. */

            CreateMap<Sprint, SprintDto>();
            CreateMap<CreateUpdateSprintDto, Sprint>();
        }
    }
}
```

## step 10 : ISprintAppService 

Next step is to define an interface for the application service. 

![Alt text](\_images\ISprintAppService.png)

Create an `ISprintAppService` interface in the `Sprints` folder (namespace) of the `SHLOKERP.Application.Contracts` project:

```c#
using System;
using System.Threading.Tasks;
using SHLOKERP.Sprints.Dtos;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;

namespace SHLOKERP.Sprints
{
    public interface ISprintAppService :
        ICrudAppService< 
            SprintDto, 
            Guid, 
            PagedAndSortedResultRequestDto,
            CreateUpdateSprintDto,
            CreateUpdateSprintDto>
    {

    }
}
```

* Defining interfaces for the application services are not required by the framework. However, it's suggested as a best practice.
* `ICrudAppService` defines common CRUD methods: `GetAsync, GetListAsync, CreateAsync, UpdateAsync and DeleteAsync`. It's not required to extend it. Instead, you could inherit from the empty `IApplicationService` interface and define your own methods manually.
* There are some variations of the `ICrudAppService` where you can use separated DTOs for each method (like using different DTOs for create and update).

## step 11 : SprintAppService
It is time to implement the `ISprintAppService` interface. 

![Alt text](\_images\SprintAppService.png)

Create a new class, named `SprintAppService` in the `Sprints` namespace (folder) of the `SHLOKERP.Application` project:

```c#
using System;
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
           
        }
    }
}
```

* `SprintAppService` is derived from `CrudAppService<...>` which implements all the CRUD (create, read, update, delete) methods defined by the `ICrudAppService`.
* `SprintAppService` injects `IRepository<Sprint, Guid>` which is the default repository for the `Sprint` entity. ABP automatically creates default repositories for each aggregate root (or entity). 
* `SprintAppService` uses `IObjectMapper` service to map `Sprint` objects to SprintDto objects and `CreateUpdateSprintDto` objects to `Sprint` objects. The Startup template uses the `AutoMapper` library as the object mapping provider. We have defined the mappings before, so it will work as expected.


## step 12 : Auto API Controllers
In a typical ASP.NET Core application, you create API Controllers to expose application services as HTTP API endpoints. This allows browsers or 3rd-party clients to call them over HTTP.

ABP can automagically configures your application services as MVC API Controllers by convention.

## step 13 : Swagger UI
The startup template is configured to run the Swagger UI using the `Swashbuckle.AspNetCore` library. Run the application (`SHLOKERP.Web`) by pressing `CTRL+F5` and navigate to `https://localhost:<port>/swagger/` on your browser. 
 
Replace <port> with your own port number.

You will see some built-in service endpoints as well as the Sprint service and its REST-style endpoints:

![alt text](\_images\SwaggerAPI.png 'SwaggerAPI')

Swagger has a nice interface to test the APIs.

If you try to execute the `[GET] /api/app/Sprint` API to get a list of Sprints, the server returns a JSON result.

## The Sprint List Page <!-- {docsify-ignore} -->
## step 14 : Localization
Before starting to the UI development, we first want to prepare the localization texts (you normally do this when needed while developing your application).

Localization texts are located under the `Localization/SHLOKERP` folder of the `SHLOKERP.Domain.Shared` project:

![alt text](\_images\Localization.png 'Localization')

Open the en.json (the English translations) file and change the content as below:


```json
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
    "SprintDeletionConfirmationMessage": "Are you sure to delete the sprint {0}?"
    }
}
```

* Localization key names are arbitrary. You can set any name. We prefer some conventions for specific text types;
    * Add `Menu:` prefix for menu items.
    * Use `Enum:<enum-type>:<enum-value>` naming convention to localize the enum members. When you do it like that, ABP can automatically localize the enums in some proper cases.

If a text is not defined in the localization file, it fallbacks to the localization key (as ASP.NET Core's standard behavior).

## step 15 : Create a Sprints Page
It's time to create something visible and usable! Instead of classic MVC, we will use the `Razor Pages UI` approach which is recommended by Microsoft.

Create `Sprints` folder under the `Pages` folder of the `SHLOKERP.Web` project. Add a new Razor Page by right clicking the `Sprints` folder then selecting Add > Razor Page menu item. Name it as `Index`:

![alt text](\_images\IndexSprint.png '')

## step 16 : Add Sprints Page to the Main Menu
Open the `SHLOKERPMenuContributor` class in the `Menus` folder.

![alt text](\_images\MenuContributor.png '')

Add the following code to the end of the `ConfigureMainMenuAsync` method:

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

The final code for `SHLOKERPMenuContributor` class looks like :
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
                AddItem(
                    new ApplicationMenuItem(SHLOKERPMenus.Sprint, l["Menu:Sprint"], "/Sprints/Sprint")
                )));

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
        }
    }
}
```

Run the project, login to the application with the username `admin` and the password `1q2w3E*` and see the new menu item has been added to the main menu:

![alt text](\_images\MenuBar.png '')

When you click to the `Sprints` menu item under the `Sprint` parent, you are being redirected to the new empty `Sprints` Page.

## step 17 : Sprint List
We will use the `Datatables.net` jQuery library to show the sprint list. Datatables library completely work via AJAX, it is fast, popular and provides a good user experience.

Datatables library is configured in the startup template, so you can directly use it in any page without including any style or script file to your page.
 
 
## Index.cshtml <!-- {docsify-ignore} -->

![alt text](\_images\IndexCS.png '')

Change the `Pages/Sprints/Index.cshtml` as following:

```HTML
@page
@using Microsoft.AspNetCore.Mvc.Localization
@using SHLOKERP.Web.Pages.Sprints.Sprint
@using SHLOKERP.Localization
@using SHLOKERP.Web.Menus
@model IndexModel
@inject IPageLayout PageLayout
@inject IHtmlLocalizer<SHLOKERPResource> L
@section scripts
{
    <abp-script src="/Pages/Sprints/Sprint/index.js" />
}

<abp-card>
    <abp-card-header>
        <abp-row>
            <abp-column size-md="_6">
                <abp-card-title>@L["Sprint"]</abp-card-title>
            </abp-column>
        </abp-row>
    </abp-card-header>
    <abp-card-body>
        <abp-table striped-rows="true" id="SprintTable" class="nowrap"/>
    </abp-card-body>
</abp-card>
```
* `abp-script` tag helper is used to add external scripts to the page. It has many additional features compared to standard `script` tag. It handles minification and versioning. See the `bundling & minification document` for details.
* `abp-card` is a tag helper for Twitter Bootstrap's `card component`. There are other useful tag helpers provided by the ABP Framework to easily use most of the bootstrap components. You could use the regular HTML tags instead of these tag helpers, but using tag helpers reduces HTML code and prevents errors by help the of IntelliSense and compile time type checking. Further information, see the `tag helpers` document.

## Index.js <!-- {docsify-ignore} -->


Create an `Index.js` file under the `Pages/Sprints` folder:

![alt text](\_images\IndexJS.png '')

The content of the file is shown below:

```Java Script
$(function () {

    var l = abp.localization.getResource('SHLOKERP');

    var service = sHLOKERP.sprints.sprint;

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
                title: l('SprintSprintName'),
                data: "sprintName"
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
});
```

* `abp.localization.getResource` gets a function that is used to localize text using the same JSON file defined in the server side. In this way, you can share the localization values with the client side.
* `abp.libs.datatables.normalizeConfiguration` is a helper function defined by the ABP Framework. There's no requirement to use it, but it simplifies the Datatables configuration by providing conventional default values for missing options.
* `abp.libs.datatables.createAjax` is another helper function to adapt ABP's dynamic JavaScript API proxies to Datatable's expected parameter format
`SHLOKERP.sprints.sprint.getList` is the dynamic JavaScript proxy function introduced before.
* `luxon` library is also a standard library that is pre-configured in the solution, so you can use to perform date/time operations easily.

## step 18 : Run the Application
You can run the application! The final UI of this part is shown below:

![alt text](\_images\CreateSprintUI.png '')

This is a fully working, server side paged, sorted and localized table of sprints.

## step 19 : Creating a New Sprint
In this section, you will learn how to create a new modal dialog form to create a new `Sprint`. The modal dialog will look like in the image below:

![alt text](\_images\CreateNewSprint.png '')

## Create the Modal Form  <!-- {docsify-ignore} -->
Create a new razor page, named `CreateModal.cshtml` under the Pages/Sprints folder of the `SHLOKERP.Web` project.

![alt text](\_images\CreateModalSprint.png '')

## CreateModal.cshtml.cs <!-- {docsify-ignore} -->

![alt text](\_images\CreateModalSprintClass.png)

Open the `CreateModal.cshtml.cs` file (CreateModalModel class) and replace with the following code:

```c#
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;
using SHLOKERP.Sprints;
using SHLOKERP.Sprints.Dtos;
using SHLOKERP.Web.Pages.Sprints.Sprint.ViewModels;

namespace SHLOKERP.Web.Pages.Sprints.Sprint
{
    public class CreateModalModel : SHLOKERPPageModel
    {
        [BindProperty]
        public CreateUpdateSprintDto ViewModel { get; set; }

        private readonly ISprintAppService _service;

        public CreateModalModel(ISprintAppService service)
        {
            _service = service;
        }

        public async Task OnGetAsync()
        {
            
            ViewModel = new CreateUpdateSprintDto();

        }
        public async Task<IActionResult> OnPostAsync()
        {
           await _service.CreateAsync(ViewModel);
            return NoContent();
        }

    }
}
```
* This class is derived from the `SHLOKERPPageModel` instead of standard `PageModel`. `SHLOKERPPageModel` indirectly inherits the `PageModel` and adds some common properties & methods that can be shared in your page model classes.
* [BindProperty] attribute on the `Sprint` property binds post request data to this property.
* This class simply injects the `ISprintAppService` in the constructor and calls the `CreateAsync` method in the `OnPostAsync` handler.
* It creates a new `CreateUpdateSprintDto` object in the `OnGet` method. ASP.NET Core can work without creating a new instance like that. However, it doesn't create an instance for you and if your class has some default value assignments or code execution in the class constructor, they won't work. For this case, we set default values for some of the `CreateUpdateSprintDto` properties.

## CreateModal.cshtml <!-- {docsify-ignore} -->

![alt text](\_images\CreateModalCSHTML.png)

Open the `CreateModal.cshtml` file and paste the code below:

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

* This modal uses `abp-dynamic-form` tag helper to automatically create the form from the `CreateEditSprintViewModel` model class.
* `abp-model` attribute indicates the model object where it's the `Sprint` property in this case.
* `abp-form-content` tag helper is a placeholder to render the form controls (it is optional and needed only if you have added some other content in the `abp-dynamic-form` tag, just like in this page).

## step 20 : Add the "New sprint" Button
Open the `Pages/Sprints/Index.cshtml` and set the content of `abp-card-header` tag as below:

```HTML
<abp-card-header>
    <abp-row>
        <abp-column size-md="_6">
            <abp-card-title>@L["Sprint"]</abp-card-title>
        </abp-column>
        <abp-column size-md="_6" class="text-right">
            <abp-button id="NewSprintButton"
                        text="@L["CreateSprint"].Value"
                        icon="plus"
                        button-type="Primary" />
        </abp-column>
    </abp-row>
</abp-card-header>
```

This adds a new button called New sprint to the top-right of the table:

![Alt text](\_images\CreateSprintButton.png)


Open the `Pages/Sprints/Index.js` and add the following code just after the Datatable configuration:

```Java Script
var createModal = new abp.ModalManager(abp.appPath + 'Sprints/Sprint/CreateModal');
    createModal.onResult(function () {
        dataTable.ajax.reload();
    });

    $('#NewSprintButton').click(function (e) {
        e.preventDefault();
        createModal.open();
    });
```

* `abp.ModalManager` is a helper class to manage modals in the client side. It internally uses Twitter Bootstrap's standard modal, but abstracts many details by providing a simple API.
* `createModal.onResult(...)` used to refresh the data table after creating a new sprint.
* `createModal.open()` is used to open the model to create a new sprint.

Now, you can run the application and add some new sprints using the new modal form.

## step 21 : Updating a Sprint

Create a new razor page, named `EditModal.cshtml` under the Pages/Sprints folder of the `SHLOKERP.Web` project:

![alt text](\_images\EditModalSprint.png)

## EditModal.cshtml.cs <!-- {docsify-ignore} -->

Open the `EditModal.cshtml.cs` file (EditModalModel class).

![alt text](\_images\EditModalSprintCSHTMLCS.png)

Replace with the following code:

```c#
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;
using SHLOKERP.Sprints;
using SHLOKERP.Sprints.Dtos;
using SHLOKERP.Web.Pages.Sprints.Sprint.ViewModels;

namespace SHLOKERP.Web.Pages.Sprints.Sprint
{
    public class EditModalModel : SHLOKERPPageModel
    {
        [HiddenInput]
        [BindProperty(SupportsGet = true)]
        public Guid Id { get; set; }

        [BindProperty]
        public CreateUpdateSprintDto ViewModel { get; set; }

        private readonly ISprintAppService _service;

        public EditModalModel(ISprintAppService service)
        {
            _service = service;
        }

        public async Task OnGetAsync()
        {
            var dto = await _service.GetAsync(Id);
            ViewModel = ObjectMapper.Map<SprintDto, CreateUpdateSprintDto>(dto);
        }

        public async Task<IActionResult> OnPostAsync()
        {
            await _service.UpdateAsync(Id, ViewModel);
            return NoContent();
        }
    }
}
```
* `[HiddenInput]` and `[BindProperty]` are standard ASP.NET Core MVC attributes. `SupportsGet` is used to be able to get `Id` value from query string parameter of the request.
* In the `OnGetAsync` method, we get `SprintDto` from the `SprintAppService` and this is being mapped to the DTO object `CreateUpdateSprintDto`.
* The `OnPostAsync` uses `SprintAppService.UpdateAsync(...)` to update the entity.

## Mapping from SprintDto to CreateUpdateSprintDto <!-- {docsify-ignore} -->

![alt text](\_images\SHLOKERPWebAutoMapperProfile.png)

To be able to map the `SprintDto` to `CreateUpdateSprintDto`, configure a new mapping. To do this, open the `SHLOKERPWebAutoMapperProfile.cs` in the `SHLOKERP.Web` project and change it as shown below:

```c#
using AutoMapper;

namespace SHLOKERP.Web
{
    public class SHLOKERPWebAutoMapperProfile : Profile
    {
        public SHLOKERPWebAutoMapperProfile()
        {
            CreateMap<SprintDto, CreateUpdateSprintDto>();
        }
    }
}
```

* We have just added `CreateMap<SprintDto, CreateUpdateSprintDto()` to define this mapping.

>Notice that we do the mapping definition in the web layer as a best practice since it is only needed in this layer.

## EditModal.cshtml <!-- {docsify-ignore} -->

![alt text](\_images\EditModalSprint.png)

Replace `EditModal.cshtml` content with the following content:

```HTML
@page
@using SHLOKERP.Localization
@using Microsoft.AspNetCore.Mvc.Localization
@using Volo.Abp.AspNetCore.Mvc.UI.Bootstrap.TagHelpers.Modal;
@inject IHtmlLocalizer<SHLOKERPResource> L
@model SHLOKERP.Web.Pages.Sprints.Sprint.EditModalModel
@{
    Layout = null;
}
<abp-dynamic-form abp-model="ViewModel" data-ajaxForm="true" asp-page="EditModal">
    <abp-modal>
        <abp-modal-header title="@L["EditSprint"].Value"></abp-modal-header>
        <abp-modal-body>
            <abp-input asp-for="Id" />
            <abp-form-content />
        </abp-modal-body>
        <abp-modal-footer buttons="@(AbpModalButtons.Cancel|AbpModalButtons.Save)"></abp-modal-footer>
    </abp-modal>
</abp-dynamic-form>
```

This page is very similar to the CreateModal.cshtml, except:

* It includes an `abp-input` for the `Id` property to store `Id` of the editing `sprint` (which is a hidden input).
* It uses Sprints/EditModal as the post URL.

## step 22 : Add "Actions" Dropdown to the Table
We will add a dropdown button to the table named *Actions*.

![alt text](\_images\EditModalSprint.png)

Open the `Pages/Sprints/Index.js` and replace the content as below:

- Added a new `ModalManager` named `editModal` to open the edit modal dialog.
    ```JavaScript
    var editModal = new abp.ModalManager(abp.appPath + 'Sprints/Sprint/EditModal');
    ```
- Added a new column at the beginning of the `columnDefs` section. This column is used for the *"Actions"* dropdown button.
    ```JavaScript
    rowAction:
    {
        items:
        [
            {
                text: l('Edit'),
                visible: abp.auth.isGranted('SHLOKERP.Sprint.Update'),
                    action: function(data) {
                    editModal.open({ id: data.record.id });
                }
            }
        ]
    }
    ```
- *"Edit"* action simply calls `editModal.open()` to open the edit dialog.
- `editModal.onResult(...)` callback refreshes the data table when you close the edit modal.
    ```JavaScript
    editModal.onResult(function () {
        dataTable.ajax.reload();
    });
    ```
The  Final `index.js` file lokk like this:
```Java Script
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
                            }
                        ]
                }
            },
            {
                title: l('SprintSprintName'),
                data: "sprintName"
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

You can run the application and edit any sprint by selecting the edit action on a sprint.

## step 23 : Deleting a Sprint

Open the Pages/Sprints/Index.js and add a new item to the rowAction items:

```Java Script
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
```
* `confirmMessage` option is used to ask a confirmation question before executing the action.
* `SHLOKERP.books.book.delete(...)` method makes an AJAX request to the server to delete a sprint.
* `abp.notify.info()` shows a notification after the delete operation.

Since we've used two new localization texts (`SprintDeletionConfirmationMessage` and `SuccessfullyDeleted`) you need to add these to the localization file 

![alt text](\_images\Localization.png)

(`en.json` under the `Localization/SHLOKERP` folder of the `SHLOKERP.Domain.Shared` project):

```c#
"SprintDeletionConfirmationMessage": "Are you sure to delete the sprint {0}?",
"SuccessfullyDeleted": "Successfully deleted!"
```

The final `Index.js` content is shown below:

```Java Script
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

You can run the application and try to delete a sprint.

The final UI looks as below:

![alt text](\_images\EditDeleteAction.png)

# step 24 : Unit Testing
To open test explorer Go to `Test` Menu -> Click in `Test Explorer`

![alt text](\_images\TestExplorer.png)

To run / debug a test case just right click on the case to be runned. you will find the options like below

![alt text](\_images\Runtestcase.png)

## App Service Testing

- Step 1 : Create a `SprintAppServiceTests` class inside the `.Application.Tests` project:

    ![alt text](\_images\SprintAppServiceTests.png)

- step 2 : Adding libraries and References

    ```c#
        using System.Threading.Tasks;
        using Shouldly;
        using Xunit;
        using Volo.Abp.Application.Dtos;
        using Volo.Abp.Validation;
        using System.Linq;
    ```
- step 3 : Implement class `SHLOKERPApplicationTestBase` 

    ```c#
    public class SprintAppServiceTests : SHLOKERPApplicationTestBase
    ```

- Step 4 : Add Dependency Injection & Write atleast one positive & Negative case for each CRUD operations.

    ```c#
    private readonly ISprintAppService _sprintAppService;

    public SprintAppServiceTests()
    {
        _sprintAppService = GetRequiredService<ISprintAppService>();
    }

    [Fact]
    public async Task Should_Get_List_Of_Sprints()
    {
        //Act
        var result = await _sprintAppService.GetListAsync(
            new PagedAndSortedResultRequestDto()
        );

        //Assert
        result.TotalCount.ShouldBeGreaterThan(0);
    }

    [Fact]
    public async Task Should_Not_Create_A_Sprint_Without_Name()
    {
        var exception = await Assert.ThrowsAsync<AbpValidationException>(async () =>
        {
            await _sprintAppService.CreateAsync(
                new CreateUpdateSprintDto
                {

                    SprintName = "",
                    WorkersCount = 8,
                    Duration = 7
                }
            );
        });

        exception.ValidationErrors
            .ShouldContain(err => err.MemberNames.Any(m => m == "Name"));
    } 
    ```

The final code looks like this:
```c#
using System.Threading.Tasks;
using Shouldly;
using Xunit;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Validation;
using System.Linq;

namespace SHLOKERP.Sprints
{
    public class SprintAppServiceTests : SHLOKERPApplicationTestBase
    {
        private readonly ISprintAppService _sprintAppService;

        public SprintAppServiceTests()
        {
            _sprintAppService = GetRequiredService<ISprintAppService>();
        }

        [Fact]
        public async Task Should_Get_List_Of_Sprints()
        {
            //Act
            var result = await _sprintAppService.GetListAsync(
                new PagedAndSortedResultRequestDto()
            );

            //Assert
            result.TotalCount.ShouldBeGreaterThan(0);
        }

        [Fact]
        public async Task Should_Not_Create_A_Sprint_Without_Name()
        {
            var exception = await Assert.ThrowsAsync<AbpValidationException>(async () =>
            {
                await _sprintAppService.CreateAsync(
                    new CreateUpdateSprintDto
                    {
                        
                        SprintName = "",
                        WorkersCount = 8,
                        Duration = 7
                    }
                );
            });

            exception.ValidationErrors
                .ShouldContain(err => err.MemberNames.Any(m => m == "Name"));
        }
    }
}
```

After running the test results will be automatically popped up at right side

![alt text](\_images\Testviewer.png)

### UI Testing:
### Testing the Razor Pages
- step 1 : Create a class called `Index_Tests` under `Pages/Sprints/` folder of `SHLOKERP.Web.Tests` project.

    ![alt text](\_images\SprintUITest.png)

- step 2 : Adding libraries and References

    ```c#
        using System.Threading.Tasks;
        using HtmlAgilityPack;
        using Shouldly;
        using Xunit;
    ```
- step 3 : Implement class SHLOKERPWebTestBase 

    ```c#
    public class Index_Tests : SHLOKERPWebTestBase
    ```
- step 4 : Define Test Method 

    ```c#
    [Fact]
    public async Task Should_Get_Table_Of_Sprints()
    {
        // Act

        var response = await GetResponseAsStringAsync("/Sprints/Sprint");

        //Assert

        var htmlDocument = new HtmlDocument();
        htmlDocument.LoadHtml(response);

        var tableElement = htmlDocument.GetElementbyId("SprintTable");
        tableElement.ShouldNotBeNull();
    }
    ```

The final code Looks like:

```c#
using System.Collections.Generic;
using System.Threading.Tasks;
using HtmlAgilityPack;
using SHLOKERP.Sprints.Dtos;
using Shouldly;
using Xunit;

namespace SHLOKERP.Pages.Sprints
{
    public class Index_Tests : SHLOKERPWebTestBase
    {
        [Fact]
        public async Task Should_Get_Table_Of_Sprints()
        {
            // Act

            var response = await GetResponseAsStringAsync("/Sprints/Sprint");

            //Assert

            var htmlDocument = new HtmlDocument();
            htmlDocument.LoadHtml(response);

            var tableElement = htmlDocument.GetElementbyId("SprintTable");
            tableElement.ShouldNotBeNull();

        }
    }
}
```

Run the Test case:

![alt text](\_images\SprintUITestCase1.png)


  > Reference Articles

**Development**

 1. [How to join two table? (`Lookup \ Reference table join`)](/ABPHelper/multiTableCrud)
 2. [Permissions & Authorizations](Authorization.md)
