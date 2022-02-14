# Adding Extra Properties to Users

## Step 1 : Identifying the Class to be extended

Usually `Users` class is extended from **Identity** module in APB

## Step 2 : Add Extra properties to `Identity.User`

Open the project `%namespace% .Domain.Shared`.

Click on the file, `%namespace% ModuleExtensionConfigurator.cs`

![alt text](\_images\configurator.png 'configurator')

## Step 3 : Object Extension

Paste the below code inside `ConfigureExtraProperties` function

```C#
private static void ConfigureExtraProperties()
{
    ObjectExtensionManager.Instance.Modules()
    .ConfigureIdentity(identity =>
    {
        identity.ConfigureUser(user =>
        {

            user.AddOrUpdateProperty<int>(  // Specify Datatype
                "EmployeeCode", // attribute name
                property =>
                {
                    //validation rules
                    property.Attributes.Add(new RequiredAttribute());
                }
            );
            
        });                
    });
}
```

Here I am trying to add `Employee code` attribute with datatype `int` to the users table.

### If you want to add another table (Lookup)

Here I am adding relation from `Designation` to `Users` table.

```C#
private static void ConfigureExtraProperties()
{
    ObjectExtensionManager.Instance.Modules()
    .ConfigureIdentity(identity =>
    {
        identity.ConfigureUser(user =>
        {
            user.AddOrUpdateProperty<Guid>( // Specify Datatype
                "Designation", // attribute name
                property =>
                {
                    property.UI.Lookup.Url = "/api/app/designation"; // api Controller
                    property.UI.Lookup.DisplayPropertyName = "designationName"; // Designation attribute to be shown
                }
            );
            
        });                
    });
}
```
## Step 4 : See the result

> List Result

![alt text](\_images\UITable.png 'UITable')

> Add / Update Result

![alt text](\_images\UIAdd.png 'UIAdd')

> Database result

![alt text](\_images\dbresult.png 'dbresult')

##### `Once the database is updated, the extra properties will turned out to another field in this user table`