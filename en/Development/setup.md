# Setup Your Development Environment
> **First things first! Let's setup your development environment before creating the project**.

## Pre-Requirements
The following tools should be installed on your development machine:

* Visual Studio 2019 (v16.8+) for Windows / Visual Studio for Mac. 1
* .NET Core 5.0+
* Node v12 or v14
* Yarn v1.20+ (not v2) 2 or npm v6+ (already installed with Node)

## The Solution Structure
The solution has a layered structure (based on the `Domain Driven Design`) and contains unit & integration test projects. 

## Creating the Solution
Before starting to the development, create a new solution.

You can use the `ABP CLI` to create a new project using startup template. 
Alternatively, you can directly create & download from the [Get Started](https://abp.io/get-started) page.

### CLI approach is used here:

### step 1 : First, install the ABP CLI if you haven't installed before:
Go to Command Prompt and type the following command:

```Bash
dotnet tool install -g Volo.Abp.Cli
```
>If you've already installed, you can update it using the following command:
```Bash
dotnet tool update -g Volo.Abp.Cli
```

### step 2 : Then use the abp new command in an empty folder to create a new solution:

- Create a new folder :

    ![Alt text](\_images\emptyfolder.png)
- In the `Address bar`  of the folder type "`cmd`" to open Command line terminal page

    ![Alt text](\_images\addressBar.png)
- Type the following command in the terminal and press ENTER :
    ```Bash
    abp new SHLOKERP -v 4.3.0
    ```
>Please specify the exact version given above "`4.3.0`" in order to generate all project files properly

    ![Alt text](\_images\abpnew.png)

- Once the command is executed we get a message
:

    ![Alt text](\_images\abpnewresult.png)

- we can see the generated project in the folder we created: 

    ![Alt text](\_images\project.png)

- `SHLOKERP` is the solution name, like *YourCompany.YourProduct*. You can use single level, two-levels or three-levels naming.
- This example specified the template name (`-t` or `--template` option). However, app is already the default template if you don't specify it.

## Create the Database
### Connection String <!-- {docsify-ignore} -->

- Check the connection string in the `appsettings.json` file under the `.Web` project :

    ![Alt text](\_images\appsettingsWeb.png)

- Check the connection string in the `appsettings.json` file under the `.DbMigrator` project :

    ![Alt text](\_images\appsettingsDbMigrator.png)

```c#
"ConnectionStrings": {
    "Default": "Server=DBServer;Database=SHLOKERP_NEW;User ID=sa;Password=Shlok123;"
    }
```
## Database Migrations
The solution uses the **Entity Framework Core Code First Migrations**. It comes with a `.DbMigrator` console application which applies the migrations and also seeds the initial data. It is useful in development as well as in the production environment.
`.DbMigrator project` has its own `appsettings.json`. So, if you have changed the connection string above, you should also change this one.

## The Initial Migration <!-- {docsify-ignore} -->
.DbMigrator application automatically creates the Initial migration on first run.

## Running the DbMigrator
Right click to the `.DbMigrator` project and select Set as StartUp Project

![alt text](https://raw.githubusercontent.com/abpframework/abp/rel-4.3/docs/en/images/set-as-startup-project.png)

Hit F5 (or Ctrl+F5) to run the application. It will have an output like shown below:

![alt text](https://raw.githubusercontent.com/abpframework/abp/rel-4.3/docs/en/images/db-migrator-output.png)

Initial seed data creates the `admin` user in the database (with the password is `1q2w3E*`) which is then used to login to the application. So, you need to use `.DbMigrator` at least once for a new database.


## Run the Application
Ensure that the `.Web` project is the startup project. Run the application which will open the login page in your browser:

![alt text](https://raw.githubusercontent.com/abpframework/abp/rel-4.3/docs/en/images/bookstore-login.png)

Enter `admin` as the **username** and `1q2w3E*` as the **password** to login to the application. The application is up and running. You can start developing your application based on this startup template.