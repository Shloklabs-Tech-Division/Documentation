# Expose CRUD API with Swagger

<video width="320" height="240" controls>
  <source src="./_videos/ExposeAPI.mp4" type="video/mp4">
</video>

## step 1 : Create the Domain Entity 

Domain layer in the startup template is separated into two projects:

* `SHLOKERP.Domain` contains your entities, domain services and other core domain objects.
* `SHLOKERP.Domain.Shared` contains constants, enums or other domain related objects those can be shared with clients.

So, define your entities in the domain layer (`SHLOKERP.Domain` project) of the solution. 

The main entity of the application is the Sprint. Create a Sprints folder (namespace) in the `SHLOKERP.Domain` project .

![Alt text](\_images\Sprint.png)

Add a Sprint class inside it:

```c#
using System;
using Volo.Abp.Domain.Entities.Auditing;

namespace SHLOKERP.Sprints
{
    public class Sprint : AuditedAggregateRoot<Guid>
    {
        public virtual string SprintName { get; set; }
        public virtual int WorkersCount { get; set; }
        public virtual int Duration { get; set; }
    }
}
```
* ABP Framework has two fundamental base classes for entities: `AggregateRoot` and `Entity`. Aggregate Root is a *Domain Driven Design* concept which can be thought as a root entity that is directly queried and worked on (see the entities document for more).
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

Open a command-line terminal in the directory of the `SHLOKERP.EntityFrameworkCore.DbMigrations` project and type the following command:
```command-line
dotnet ef migrations add Added_Sprint_Entity
```
This will add a new migration class to the project:

![alt text](\_images\DBmigrations.png 'DBmigrations')

## step 5 : Update the Database
Run the `SHLOKERP.DbMigrator` application to update the database:

![alt text](\_images\UpdateDatabaseMigration.png 'UpdateDatabaseMigration')

.DbMigrator is a console application that can be run to migrate the database schema and seed the data on development and production environments.



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

* DTO classes are used to transfer data between the presentation layer and the application layer. See the Data Transfer Objects document for more details.
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


  > Reference Articles

**Unit Testing**

1. [Write unit tests](/Testing)

**Development**

 1. [How to join two table? (`Lookup \ Reference table join`)](/ABPHelper/multiTableCrud)
 2. [Listing Screen](/ListingScreen)
 3. [CRUD Operations](/CRUDForSingleTable)
