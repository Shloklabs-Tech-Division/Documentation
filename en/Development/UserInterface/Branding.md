# **ASP.NET Core MVC / Razor Pages: Branding**

# **IBrandingProvider**

`IBrandingProvider` is a simple interface that is used to show the application name and logo on the layout.

### Step 1: Go to **Branding Provider Class** Present inside `%Namespace%.Web` Project:

![alt text](../_images/UserInterface/Branding.png)

### Step 2:You can implement the `IBrandingProvider` interface or inherit from the `DefaultBrandingProvider` to set the application name:


```c#
using Volo.Abp.Ui.Branding;
using Volo.Abp.DependencyInjection;

namespace MyProject.Web
{
    [Dependency(ReplaceServices = true)]
    public class MyProjectBrandingProvider : DefaultBrandingProvider
    {
        public override string AppName => "SHLOKERP";

        public override string LogoUrl => "logo.png";
    }
}
```

- **Result:**

![alt text](../_images/UserInterface/brandinglogo.png)

`IBrandingProvider` has the following properties:

- `AppName`: The application name.
- `LogoUrl`: A URL to show the application logo.
- `LogoReverseUrl`: A URL to show the application logo on a reverse color theme (dark, for example).

> **Tip**: `IBrandingProvider` is used in every page refresh. For a multi-tenant application, you can return a tenant specific application name to customize it per tenant.

# Overriding the Branding Area

You can see the [UI Customization Guide](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Customization-User-Interface) to learn how you can replace the branding area with a custom view component.