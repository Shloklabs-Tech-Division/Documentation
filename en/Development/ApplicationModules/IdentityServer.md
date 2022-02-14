# IdentityServer Module
IdentityServer module provides a full integration with the IdentityServer (IDS) framework, which provides advanced authentication features like single sign-on and API access control.

## How to Install
This module comes as pre-installed (as NuGet/NPM packages) when you create a new solution with the ABP Framework. You can continue to use it as package and get updates easily, or you can include its source code into your solution (see `get-source` [CLI](https://docs.abp.io/en/abp/latest/CLI) command) to develop your custom module.

## The Source Code
The source code of this module can be accessed [here](https://github.com/abpframework/abp/tree/dev/modules/identityserver). 

## User Interface
This module implements the domain logic and database integrations, but not provides any UI. Management UI is useful if you need to add clients and resources on the fly.

### Relations to Other Modules
This module is based on the Identity Module and have an integration package with the [Account Module](AccountModule.md).

# Options
## AbpIdentityServerBuilderOptions
`AbpIdentityServerBuilderOptions` can be configured in `PreConfigureServices` method of your `Identity Server module`. 

Example:

```c#
public override void PreConfigureServices(ServiceConfigurationContext context)
{
	PreConfigure<AbpIdentityServerBuilderOptions>(builder =>
	{
    	//Set options here...		
	});
}
```
`IIdentityServerBuilder` can be configured in `PreConfigureServices` method of your Identity Server module. 

Example:

```c#
public override void PreConfigureServices(ServiceConfigurationContext context)
{
	PreConfigure<IIdentityServerBuilder>(builder =>
	{
    	builder.AddSigningCredential(...);	
	});
}
```

>click [here](https://docs.abp.io/en/abp/latest/Modules/IdentityServer#internals) to know about **Internals of IdentityServer Module**.