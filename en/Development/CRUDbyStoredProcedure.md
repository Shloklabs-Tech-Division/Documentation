>In this section, you will create an application service to Insert, update and delete Entity using the Stored Procedure.

<video width="320" height="240" controls>
  <source src="./_videos/CRUDUsingStoredProcedure.mp4" type="video/mp4">
</video>

## step 1 : Create the Domain Entity 

Domain layer in the startup template is separated into two projects:

* `SHLOKERP.Domain` contains your entities, domain services and other core domain objects.
* `SHLOKERP.Domain.Shared` contains constants, enums or other domain related objects those can be shared with clients.

So, define your entities in the domain layer (`SHLOKERP.Domain` project) of the solution. 

The entity of the application is the `Developer`. Create a `Developers` folder (namespace) in the `SHLOKERP.Domain` project .

![Alt text](\_images\Developer.png)

Add a Developer class inside it:

```c#
using System;
using Volo.Abp.Domain.Entities.Auditing;

namespace SHLOKERP.Developers
{
    public class Developer : AuditedAggregateRoot<Guid>
    {
        public string Name { get; set; }
        public int EmployeeId { get; set; }
        public DateTime DateOfBirth { get; set; }
    }
}
```
And all other necessary steps to create CRUD Operation.

![Alt text](\_images\DeveloperAppService.png).

Refer [CRUD (Single Table)](CRUDForSingleTable.md) for CRUD Operation.

## step 2 Create Stored Procedures For Insert Update Delete Operation for `Developer` Entity in MSSql:

### Stored Procedure for Insert Operation
```sql
CREATE PROCEDURE [dbo].[sp_InsertDeveloperInfo]
-- Add the parameters for the stored procedure here
@Id uniqueidentifier,
@Name nvarchar(MAX),
@EmployeeId int ,
@DateOfBirth datetime2(7),
@CreationTime datetime2(7)

AS
    BEGIN
-- SET NOCOUNT ON added to prevent extra result sets from
-- interfering with SELECT statements.
SET NOCOUNT ON;

    INSERT INTO [SHLOKERP_NEW].[dbo].[AppDevelopers]([Id],[Name],[EmployeeId],[DateOfBirth],[CreationTime])
    VALUES(@Id,@Name, @EmployeeId, @DateOfBirth,@CreationTime)
END
```

### Stored Procedure for Update Operation
```sql
CREATE PROCEDURE [dbo].[sp_UpdateDeveloper]
    -- Add the parameters for the stored procedure here
	@Id uniqueidentifier,
    @Name nvarchar(MAX),
    @EmployeeId int ,
	@DateOfBirth datetime2(7)
AS
BEGIN
    -- SET NOCOUNT ON added to prevent extra result sets from
    -- interfering with SELECT statements.
    SET NOCOUNT ON;

    Update [SHLOKERP_NEW].[dbo].[AppDevelopers]
    set Name = @Name,EmployeeId = @EmployeeId,DateOfBirth = @DateOfBirth
    where Id = @Id;
END
```
### Stored Procedure for Delete Operation

## step 3 Adding Helper Methods and Stored Procedure: 

Create a custom Repository `DeveloperRepository.cs`:

![Alt text](\_images\DeveloperRepository.png)

>If [Code Generation with ABPHelper](AbpHelperCodeGen.md) is used for generation of Entity, no need of creating a custom repository since `AbpHelper` takes care of it.

Add Helper Methods to `DeveloperRepository.cs` , refer [Stored Procedure](StoredProcedure.md) step 3.

Here is a stored procedure call that Creates `Developer` of all `Developers`. Added this to the repository implementation (`DeveloperRepository`).

Add the following code in `DeveloperRepository.cs` class created inside `Developer` Folder :

- Code for CreateDeveloper stored procedure call
    ```c#
    public async Task CreateDeveloper(CreateUpdateDeveloperDto Dto)
    {
        Guid i = Guid.NewGuid();
        CancellationToken cancellationToken = default;
        await DbContext.Database.ExecuteSqlRawAsync(
            "EXEC sp_InsertDeveloperInfo @Id, @Name,@EmployeeId,@DateOfBirth,@CreationTime",
            new List<object> { new SqlParameter("Id", i), new SqlParameter("Name", Dto.Name), new SqlParameter("EmployeeId", Dto.EmployeeId), new SqlParameter("DateOfBirth", Dto.DateOfBirth), new SqlParameter("CreationTime", DateTime.Now) },
            cancellationToken);

    }
    ```
- Code for UpdateDeveloper stored procedure call
    ```c#
    public async Task UpdateDeveloper(Guid id, CreateUpdateDeveloperDto Dto)
    {
        CancellationToken cancellationToken = default;
        await DbContext.Database.ExecuteSqlRawAsync(
            "EXEC sp_UpdateDeveloper @Id, @Name,@EmployeeId,@DateOfBirth",
            new List<object> { new SqlParameter("Id", id), new SqlParameter("Name", Dto.Name), new SqlParameter("EmployeeId", Dto.EmployeeId), new SqlParameter("DateOfBirth", Dto.DateOfBirth) },
            cancellationToken);
    }
    ```

The Whole class looks like this:
```c#
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Data.SqlClient;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Storage;
using SHLOKERP.Developers.Dtos;
using SHLOKERP.EntityFrameworkCore;
using Volo.Abp.Domain.Repositories.EntityFrameworkCore;
using Volo.Abp.EntityFrameworkCore;

namespace SHLOKERP.Developers
{
    public class DeveloperRepository : EfCoreRepository<SHLOKERPDbContext, Developer, Guid>, IDeveloperRepository
    {
        public DeveloperRepository(IDbContextProvider<SHLOKERPDbContext> dbContextProvider) : base(dbContextProvider)
        {

        }
        public async Task UpdateDeveloper(Guid id,CreateUpdateDeveloperDto Dto)
        {
            CancellationToken cancellationToken = default;
            await DbContext.Database.ExecuteSqlRawAsync(
                "EXEC sp_UpdateDeveloper @Id, @Name,@EmployeeId,@DateOfBirth",
                new List<object> { new SqlParameter("Id", id), new SqlParameter("Name",Dto.Name ), new SqlParameter("EmployeeId", Dto.EmployeeId), new SqlParameter("DateOfBirth", Dto.DateOfBirth) },
                cancellationToken);
        }
        public async Task CreateDeveloper(CreateUpdateDeveloperDto Dto)
        {
            Guid i = Guid.NewGuid();
            CancellationToken cancellationToken = default;
            await DbContext.Database.ExecuteSqlRawAsync(
                "EXEC sp_InsertDeveloperInfo @Id, @Name,@EmployeeId,@DateOfBirth,@CreationTime",
                new List<object> { new SqlParameter("Id", i), new SqlParameter("Name", Dto.Name), new SqlParameter("EmployeeId", Dto.EmployeeId), new SqlParameter("DateOfBirth", Dto.DateOfBirth), new SqlParameter("CreationTime", DateTime.Now) },
                cancellationToken);

        }

        private DbCommand CreateCommand(string commandText, CommandType commandType, params SqlParameter[] parameters)
        {
            var command = DbContext.Database.GetDbConnection().CreateCommand();
            var y = GetDbContextAsync();

            command.CommandText = commandText;
            command.CommandType = commandType;
            command.Transaction = DbContext.Database.CurrentTransaction?.GetDbTransaction();

            foreach (var parameter in parameters)
            {
                command.Parameters.Add(parameter);
            }

            return command;
        }

        private async Task EnsureConnectionOpenAsync(CancellationToken cancellationToken = default)
        {
            var connection = DbContext.Database.GetDbConnection();

            if (connection.State != ConnectionState.Open)
            {
                await connection.OpenAsync(cancellationToken);
            }
        }
    }
}
```

## step 4 : Define the StoredProcedure methods in the `IDeveloperRepository`

Implement the interface in domain layer(`SHLOKERP.Domain`)

![Alt text](\_images\IDeveloperRepository.png)

```c#
using SHLOKERP.Developers.Dtos;
using System;
using System.Threading.Tasks;
using Volo.Abp.Domain.Repositories;

namespace SHLOKERP.Developers
{
    public interface IDeveloperRepository : IRepository<Developer, Guid>
    {
        Task UpdateDeveloper(Guid id,CreateUpdateDeveloperDto input);
        Task CreateDeveloper(CreateUpdateDeveloperDto input);
    }
}
```

## step 4 : Use it in Application Service
Now we implemented the function that calls stored procedure from database. Let's use it in application service:

![Alt text](\_images\DeveloperAppService.png)

```c#
using System;
using SHLOKERP.Permissions;
using SHLOKERP.Developers.Dtos;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;
using System.Threading.Tasks;

namespace SHLOKERP.Developers
{
    public class DeveloperAppService : CrudAppService<Developer, DeveloperDto, Guid, PagedAndSortedResultRequestDto, CreateUpdateDeveloperDto, CreateUpdateDeveloperDto>,
        IDeveloperAppService
    {
        protected override string GetPolicyName { get; set; } = SHLOKERPPermissions.Developer.Default;
        protected override string GetListPolicyName { get; set; } = SHLOKERPPermissions.Developer.Default;
        protected override string CreatePolicyName { get; set; } = SHLOKERPPermissions.Developer.Create;
        protected override string UpdatePolicyName { get; set; } = SHLOKERPPermissions.Developer.Update;
        protected override string DeletePolicyName { get; set; } = SHLOKERPPermissions.Developer.Delete;

        private readonly IDeveloperRepository _repository;
        
        public DeveloperAppService(IDeveloperRepository repository) : base(repository)
        {
            _repository = repository;
        }

        public async Task UpdateDeveloper(Guid id,CreateUpdateDeveloperDto input)
        {
            await _repository.UpdateDeveloper(id,input);
        }

        public async Task CreateDeveloper(CreateUpdateDeveloperDto input)
        {
            await _repository.CreateDeveloper(input);
        }
    }
}
```
Now,implement the task method that calls stored procedure from repository into the AppService Interface
`IDeveloperAppService.cs`

![Alt text](\_images\IDeveloperAppService.png)

```c#
using System;
using System.Threading.Tasks;
using SHLOKERP.Developers.Dtos;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;

namespace SHLOKERP.Developers
{
    public interface IDeveloperAppService :
        ICrudAppService< 
            DeveloperDto, 
            Guid, 
            PagedAndSortedResultRequestDto,
            CreateUpdateDeveloperDto,
            CreateUpdateDeveloperDto>
    {
        Task UpdateDeveloper(Guid id, CreateUpdateDeveloperDto input);
        
        Task CreateDeveloper(CreateUpdateDeveloperDto input);
    }
}
```


## step 5 : Using the stored procedure calls instead of `CRUDAppService` calls in `SHLOKERP.Web`:

For Creating Pages Refer [CRUD (Single Table)](CRUDForSingleTable.md) from step 12 onwards.

![Alt text](\_images\DeveloperPage.png)

- Replace the code in `CreateModal.cshtml.cs` of `Developer` Entity under the `Pages/Developers` folder of the `SHLOKERP.Web` project:

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SHLOKERP.Developers;
using SHLOKERP.Developers.Dtos;
using SHLOKERP.Web.Pages.Developers.Developer.ViewModels;

namespace SHLOKERP.Web.Pages.Developers.Developer
{
    public class CreateModalModel : SHLOKERPPageModel
    {
        [BindProperty]
        public CreateEditDeveloperViewModel ViewModel { get; set; }

        private readonly IDeveloperAppService _service;

        public CreateModalModel(IDeveloperAppService service)
        {
            _service = service;
        }
        
        public virtual async Task<IActionResult> OnPostAsync()
        {
            var dto = ObjectMapper.Map<CreateEditDeveloperViewModel, CreateUpdateDeveloperDto>(ViewModel);
            await _service.CreateDeveloper(dto);
            
            return NoContent();
        }
    }
}
```

- Replace the code in `EditModal.cshtml.cs` of `Developer` Entity under the `Pages/Developers` folder of the `SHLOKERP.Web` project:

```c#
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SHLOKERP.Developers;
using SHLOKERP.Developers.Dtos;
using SHLOKERP.Web.Pages.Developers.Developer.ViewModels;

namespace SHLOKERP.Web.Pages.Developers.Developer
{
    public class EditModalModel : SHLOKERPPageModel
    {
        [HiddenInput]
        [BindProperty(SupportsGet = true)]
        public Guid Id { get; set; }

        [BindProperty]
        public CreateEditDeveloperViewModel ViewModel { get; set; }

        private readonly IDeveloperAppService _service;

        public EditModalModel(IDeveloperAppService service)
        {
            _service = service;
        }

        public virtual async Task OnGetAsync()
        {
            var dto = await _service.GetAsync(Id);
            ViewModel = ObjectMapper.Map<DeveloperDto, CreateEditDeveloperViewModel>(dto);
        }

        public virtual async Task<IActionResult> OnPostAsync()
        {
            var dto = ObjectMapper.Map<CreateEditDeveloperViewModel, CreateUpdateDeveloperDto>(ViewModel);
            await _service.UpdateDeveloper(Id, dto);
            return NoContent();
        }
    }
}
```

On execution of these tasks the Stored Procedure calls defined in `DeveloperAppServices` is called instead of `CRUDAppServices` default CreateAsync,UpdateAsync methods.

![Alt text](\_images\CRUDSP.png)