# Stored Procedure<!-- {docsify-ignore} -->

<video width="320" height="240" controls>
  <source src="./videos/SP.mp4" type="video/mp4">
</video>

>Create custom repositories and use stored procedure, view, user defined functions.

## step 0 : Create Stored Procedure for `Sprint` Entity in MSSql

- Creating Stored Procedure 
    ```sql
    --GetSprintnames procedure
    CREATE PROCEDURE [dbo].[GetSprintnames]
    AS
    BEGIN
	SET NOCOUNT ON;
	SELECT SprintName FROM dbo.AppSprints
    END
    ```
- on Executing stored procedure `GetSprintnames`
    ```sql
    EXEC GetSprintnames
    ```
    we get

    ![Alt text](\_images\SPMssql.png)



## step 1 : Create the Domain Entity 

Define your entities in the domain layer (`SHLOKERP.Domain` project) of the solution. 

![Alt text](\_images\Sprint.png)

Here we are using `Sprint` as our Domain Entity.

## Step 2 : Creating A Custom Repository

First of all, We will create a custom repository to do some basic operations on `Sprint` entity using stored procedure, view and user defined function. To implement a custom repository, just derive from your application specific base repository class.

Implement the repository in infrastructure layer (`SHLOKERP.EntityFrameworkCore`).

![Alt text](\_images\SprintRepository.png)

Add the following code in `SprintRepository.cs` class created inside `Sprint` Folder :
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
using SHLOKERP.EntityFrameworkCore;
using Volo.Abp.Domain.Repositories.EntityFrameworkCore;
using Volo.Abp.EntityFrameworkCore;

namespace SHLOKERP.Sprints
{
    public class SprintRepository : EfCoreRepository<SHLOKERPDbContext, Sprint, Guid>, ISprintRepository
    {
        public SprintRepository(IDbContextProvider<SHLOKERPDbContext> dbContextProvider) : base(dbContextProvider)
        {
            
        }
    }
}
```
## step 3 : Add Helper Methods
We are creating some helper methods that will be shared by other methods to perform some common tasks.

Add the following code in `SprintRepository.cs` class created inside `Sprint` Folder :
```c#
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
```
## step 4 : Stored Procedure
Here is a stored procedure call that gets `Sprintnames` of all `Sprints`. Added this to the repository implementation (`SprintRepository`).

Add the following code in `SprintRepository.cs` class created inside `Sprint` Folder :
```c#
CancellationToken cancellationToken = default;
public async Task<List<string>> GetSprintNames()
{
    await EnsureConnectionOpenAsync(cancellationToken);

    using (var command = CreateCommand("GetSprintnames", CommandType.StoredProcedure))
    {
        using (var dataReader = await command.ExecuteReaderAsync(cancellationToken))
        {
            var result = new List<string>();

            while (await dataReader.ReadAsync(cancellationToken))
            {
                result.Add(dataReader["SprintName"].ToString());
            }

            return result;
        }
    }
}
```

The Final code for `SprintRepository` class looks like:
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
using SHLOKERP.EntityFrameworkCore;
using Volo.Abp.Domain.Repositories.EntityFrameworkCore;
using Volo.Abp.EntityFrameworkCore;

namespace SHLOKERP.Sprints
{
    public class SprintRepository : EfCoreRepository<SHLOKERPDbContext, Sprint, Guid>, ISprintRepository
    {
        public SprintRepository(IDbContextProvider<SHLOKERPDbContext> dbContextProvider) : base(dbContextProvider)
        {
            
        }
        CancellationToken cancellationToken = default;
        public async Task<List<string>> GetSprintNames()
        {
            await EnsureConnectionOpenAsync(cancellationToken);

            using (var command = CreateCommand("GetSprintnames", CommandType.StoredProcedure))
            {
                using (var dataReader = await command.ExecuteReaderAsync(cancellationToken))
                {
                    var result = new List<string>();

                    while (await dataReader.ReadAsync(cancellationToken))
                    {
                        result.Add(dataReader["SprintName"].ToString());
                    }

                    return result;
                }
            }
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

## step 5 : Define the StoredProcedure methods in the `ISprintRepository`

Implement the interface in domain layer(`SHLOKERP.Domain`)

![Alt text](\_images\ISprintRepository.png)

Add the following code in `ISprintRepository.cs` created inside `Sprints` Folder:

```c#
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Volo.Abp.Domain.Repositories;

namespace SHLOKERP.Sprints
{
    public interface ISprintRepository : IRepository<Sprint, Guid>
    {
        Task<List<string>> GetSprintNames();
    }
}
```

## step 5 : Use it in Application Service
Now we implemented the function that calls stored procedure from database. Let's use it in application service:

Create a class called `SprintAppService.cs` in `SHLOKERP.Application` Project under folder called `Sprints`.

![Alt text](\_images\SprintAppService.png)

Add the following code:

```c#
public async Task<List<string>> GetSprintNames()
{
    var SprintNames = await _repository.GetSprintNames();
    return SprintNames;
}
```
The Final code looks like:
```c#
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using SHLOKERP.Permissions;
using SHLOKERP.Revenues;
using SHLOKERP.Sprints.Dtos;
using Volo.Abp.Application.Dtos;
using Volo.Abp.Application.Services;
using Volo.Abp.Domain.Entities;
using Volo.Abp.Domain.Repositories;
using System.Linq;
using System.Linq.Dynamic.Core;

namespace SHLOKERP.Sprints
{
    [Authorize(SHLOKERPPermissions.Sprint.Default)]
    public class SprintAppService : CrudAppService
        <Sprint, SprintDto, Guid, PagedAndSortedResultRequestDto, CreateUpdateSprintDto, CreateUpdateSprintDto>,
        ISprintAppService
    {
        protected override string GetPolicyName { get; set; } = SHLOKERPPermissions.Sprint.Default;
        protected override string GetListPolicyName { get; set; } = SHLOKERPPermissions.Sprint.Default;
        protected override string CreatePolicyName { get; set; } = SHLOKERPPermissions.Sprint.Create;
        protected override string UpdatePolicyName { get; set; } = SHLOKERPPermissions.Sprint.Update;
        protected override string DeletePolicyName { get; set; } = SHLOKERPPermissions.Sprint.Delete;

        private readonly ISprintRepository _repository;
        private readonly IRevenueRepository _revenueRepository;


        public SprintAppService(IRepository<Sprint, Guid> repository,
            IRevenueRepository revenueRepository, ISprintRepository sprintrepository) : base(repository)
        {
            _revenueRepository = revenueRepository;
            _repository = sprintrepository;
        }

        public override async Task<SprintDto> GetAsync(Guid id)
        {
            //Get the IQueryable<Sprint> from the repository
            var queryable = await Repository.GetQueryableAsync();

            //Prepare a query to join sprints and revenues
            var query = from sprint in queryable
                        join revenue in _revenueRepository on sprint.RevenueId equals revenue.Id
                        where sprint.Id == id
                        select new { sprint, revenue };

            //Execute the query and get the sprint with revenue
            var queryResult = await AsyncExecuter.FirstOrDefaultAsync(query);
            if (queryResult == null)
            {
                throw new EntityNotFoundException(typeof(Sprint), id);
            }

            var sprintDto = ObjectMapper.Map<Sprint, SprintDto>(queryResult.sprint);
            sprintDto.RevenueIncome = queryResult.revenue.Income;
            return sprintDto;
        }

        public override async Task<PagedResultDto<SprintDto>> GetListAsync(PagedAndSortedResultRequestDto input)
        {
            //Get the IQueryable<Sprint> from the repository
            var queryable = await Repository.GetQueryableAsync();

            //Prepare a query to join sprints and revenues
            var query = from sprint in queryable
                        join revenue in _revenueRepository on sprint.RevenueId equals revenue.Id
                        select new { sprint, revenue };

            // Paging
            query = query
                .OrderBy(NormalizeSorting(input.Sorting))
                .Skip(input.SkipCount)
                .Take(input.MaxResultCount);

            //Execute the query and get a list
            var queryResult = await AsyncExecuter.ToListAsync(query);

            //Convert the query result to a list of BookDto objects
            var sprintDtos = queryResult.Select(x =>
            {
                var sprintDto = ObjectMapper.Map<Sprint, SprintDto>(x.sprint);
                sprintDto.RevenueIncome = x.revenue.Income;
                return sprintDto;
            }).ToList();

            //Get the total count with another query
            var totalCount = await Repository.GetCountAsync();

            return new PagedResultDto<SprintDto>(
                totalCount,
                sprintDtos
            );
        }

        public async Task<ListResultDto<RevenueLookUpDto>> GetRevenueLookUpAsync()
        {
            var revenues = await _revenueRepository.GetListAsync();

            return new ListResultDto<RevenueLookUpDto>(
                ObjectMapper.Map<List<Revenue>, List<RevenueLookUpDto>>(revenues)
            );
        }

        private static string NormalizeSorting(string sorting)
        {
            if (sorting.IsNullOrEmpty())
            {
                return $"sprint.{nameof(Sprint.RevenueId)}";
            }

            if (sorting.Contains("revenueIncome", StringComparison.OrdinalIgnoreCase))
            {
                return sorting.Replace(
                    "revenueIncome",
                    "revenue.Income",
                    StringComparison.OrdinalIgnoreCase
                );
            }
            return $"sprint.{sorting}";
        }

        public async Task<List<string>> GetSprintNames()
        {
            var SprintNames = await _repository.GetSprintNames();
            return SprintNames;
        }
    }
}
```

On executing `GetSprintNames()`, the stored procedure `GetSprintnames` that we have created in MSsql is called and it returns such result:

![Alt text](\_images\SPEg.png)

We can render these results in view. Refer [Listing Screen](ListingScreen.md).