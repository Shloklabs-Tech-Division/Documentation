# The Application Startup Template

<video width="320" height="240" controls>
  <source src="./_videos/UnitTesting.mp4" type="video/mp4">
</video>

## The Test Projects
See the following solution structure in the Visual Studio:

![Alt text](\_images\TestProjects.png)

## The Test Infrastructure
The startup solution has the following libraries already installed;

- `xUnit` as the test framework.
- `NSubstitute` as the mocking library.
- `Shouldly` as the assertion library.
While you are free to replace them with your favorite tools, this document and examples will be base on these tooling.

## The Test Explorer
You can use the Test Explorer to view and run the tests in Visual Studio. For other IDEs, see their own documentation.

## Open the Test Explorer
- Open the Test Explorer, under the Tests menu, if it is not already open:

    To open test explorer Go to `Test` Menu -> Click in `Test Explorer`

    ![alt text](\_images\TestExplorer.png)

## Run Tests In Parallel
- The test infrastructure is compatible to run the tests in parallel. It is **strongly suggested** to run all the tests in parallel, which is pretty faster then running them one by one.

    To enable it, click to the caret icon near to the settings (gear) button and select the *Run Tests In Parallel*.

    ![Alt text](\_images\vs-run-tests-in-parallel.png)


- To run / debug a test case just right click on the case to be runned. you will find the options like below

    ![alt text](\_images\Runtestcase.png)

## Testing Listing Screen

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


## Testing CRUD

Consider `Project` Entity.
`CRUD operation` is created as per [CRUD (Single Table)](CRUDForSingleTable.md) steps.

```c#
using Volo.Abp.Domain.Entities.Auditing;
using System;

namespace SHLOKERP.Projects
{
    public class Project : AuditedAggregateRoot<Guid>
    {
        public string ProjectName { get; set; }
        public int ResourcesNeeded { get; set; }
        public int Budget { get; set; }
    }
}
```

![Alt text](\_images\ProjectAppServiceTest.png)

Add a new test class, named `ProjectAppServiceTest` in the `Projects` namespace (folder) of the `SHLOKERP.Application.Tests` project:

- Method to **get and check** the list of projects

```c#
    [Fact]
    public async Task Should_Get_List_Of_Projects()
    {
        //Act
        var result = await _ProjectAppService.GetListAsync(
            new PagedAndSortedResultRequestDto()
        );

        //Assert
        result.TotalCount.ShouldBeGreaterThan(0);
        result.Items.ShouldContain(b => b.ProjectName == "Test");
    }
```

- Test Method that **creates** a new valid `Project`

```c#
[Fact]
public async Task Should_Create_A_Valid_Project()
{
    //Act
    var result = await _ProjectAppService.CreateAsync(
        new CreateUpdateProjectDto
        {
            ProjectName = "New Test Project",
            ResourcesNeeded = 11,
            Budget = 1300
        }
    );

    //Assert
    result.Id.ShouldNotBe(Guid.Empty);
    result.ProjectName.ShouldBe("New Test Project");
}
```

- Test Method that tries to create an invalid project and fails:

```c#
[Fact]
public async Task Should_Not_Create_A_Project_Without_ProjectName()
{
    var exception = await Assert.ThrowsAsync<AbpValidationException>(async () =>
    {
        await _ProjectAppService.CreateAsync(
            new CreateUpdateProjectDto
            {
                ProjectName = "",
                ResourcesNeeded = 11,
                Budget = 1300
            }
        );
    });

    exception.ValidationErrors
        .ShouldContain(err => err.MemberNames.Any(m => m == "Name"));
}
```
The final test class should be as shown below: 

```c#
using System;
using System.Linq;
using System.Threading.Tasks;
using Shouldly;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Validation;
using Xunit;

namespace SHLOKERP.Projects
{
    public class ProjectAppServiceTests : SHLOKERPApplicationTestBase
    {
        private readonly IProjectAppService _ProjectAppService;

        public ProjectAppServiceTests()
        {
            _ProjectAppService = GetRequiredService<IProjectAppService>();
        }

        [Fact]
        public async Task Should_Get_List_Of_Projects()
        {
            //Act
            var result = await _ProjectAppService.GetListAsync(
                new PagedAndSortedResultRequestDto()
            );

            //Assert
            result.TotalCount.ShouldBeGreaterThan(0);
            result.Items.ShouldContain(b => b.ProjectName == "Test");
        }

        [Fact]
        public async Task Should_Create_A_Valid_Project()
        {
            //Act
            var result = await _ProjectAppService.CreateAsync(
                new CreateUpdateProjectDto
                {
                    ProjectName = "New Test Project",
                    ResourcesNeeded = 11,
                    Budget = 1300
                }
            );

            //Assert
            result.Id.ShouldNotBe(Guid.Empty);
            result.ProjectName.ShouldBe("New Test Project");
        }

        [Fact]
        public async Task Should_Not_Create_A_Project_Without_ProjectName()
        {
            var exception = await Assert.ThrowsAsync<AbpValidationException>(async () =>
            {
                await _ProjectAppService.CreateAsync(
                    new CreateUpdateProjectDto
                    {
                        ProjectName = "",
                        ResourcesNeeded = 11,
                        Budget = 1300
                    }
                );
            });

            exception.ValidationErrors
                .ShouldContain(err => err.MemberNames.Any(m => m == "Name"));
        }
    }
}
```

Run the Test case:

![Alt text](\_images\ProjectAppServiceTestCase1,2.png)

## UI Testing

### Testing the Razor Pages
- step 1 : Create a class called `Index_Tests` under `Pages/Sprints/` folder of `SHLOKERP.Web.Tests` project.

    ![alt text](\_images\SprintUITest.png)

- step 2 : Adding libraries and References

    ```c#
        using System.Collections.Generic;
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



