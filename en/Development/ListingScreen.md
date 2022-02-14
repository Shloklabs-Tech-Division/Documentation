# Creating the List Screen with (search, sort, paging, serverside)

<video width="320" height="240" controls>
  <source src="./_videos/ListingScreen.mp4" type="video/mp4">
</video>

> For All the backend API works please follow [Exposing API](ExposeAPI).

## The Sprint List Page <!-- {docsify-ignore} -->
## step 1 : Localization
Before starting to the UI development, we first want to prepare the localization texts (you normally do this when needed while developing your application).

Localization texts are located under the Localization/`SHLOKERP` folder of the `SHLOKERP.Domain.Shared` project:

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

## step 2 : Create a Sprints Page
It's time to create something visible and usable! Instead of classic MVC, we will use the `Razor Pages UI` approach which is recommended by Microsoft.

Create `Sprints` folder under the `Pages` folder of the `SHLOKERP.Web` project. Add a new Razor Page by right clicking the `Sprints` folder then selecting Add > Razor Page menu item. Name it as `Index`:

![alt text](\_images\IndexSprint.png '')

## step 3 : Add Sprints Page to the Main Menu
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

Run the project, login to the application with the username `admin` and the password `1q2w3E*` and see the new menu item has been added to the main menu:

![alt text](\_images\MenuBar.png '')

When you click to the `Sprints` menu item under the `Sprint` parent, you are being redirected to the new empty `Sprints` Page.

## step 4 : Sprint List
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

## step 5 : Run the Application
You can run the application! The final UI of this part is shown below:

![alt text](\_images\CreateSprintUI.png '')

This is a fully working, server side paged, sorted and localized table of sprints.

## Step 6 : Unit Testing
To open test explorer Go to `Test` Menu -> Click in `Test Explorer`

![alt text](\_images\TestExplorer.png)

To run / debug a test case just right click on the case to be runned. you will find the options like below

![alt text](\_images\Runtestcase.png)

### Testing AppService

- Step 1 : Create a `SprintAppServiceTests` class inside the `.Application.Tests` project:

    ![alt text](\_images\SprintAppServiceTests.png)

- step 2 : Write atleast one positive & Negative case for Listing the data from table.

    Example:
    ```c#
    using Abp.Runtime.Validation;
    using SHLOKERP.Sprints.Dtos;
    using Shouldly;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Volo.Abp.Application.Dtos;
    using Xunit;

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

**Unit Testing**

1. [Write unit tests](/Testing)

**Menu**

1. [Menu Grouping](/MenuConfig?id=grouping)
2. [Add Icon to menu](/MenuConfig?id=adding-icon-to-menu-items)
3. [Add permission to menu](/MenuConfig?id=adding-permission-is-easy)
4. [Reorder Menus](/MenuConfig?id=reorder-add-remove-default-module-menus-tenant-users-roles)

**Development**

 1. [Exposing API](/ExposeAPI)
 2. [How to join two table? (`Lookup \ Reference table join`)](/ABPHelper/multiTableCrud)
 3. [CRUD Operations](/CRUDForSingleTable)
