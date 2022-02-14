# Adding a Extenal JS plugins

> In this article, we are going to see how to include Extenal JS Plugins, Now we are going to include Full Calendar Plugin

## Step 1 : Adding the plugin files.

First Download the source of the plugin and add it to `Web` Project inside `wwwroot/libs/`.

![lib path](../_images/frontend/libpath.png)

## Step 2 : Including this scripts inside the cshtml file.

Use @Section blocks to have your Scripts loading like below,

![lib path](../_images/frontend/scripts.png)

## Step 3 : UI Part

Just add the `div` with id `calendar`.

![lib path](../_images/frontend/calendardiv.png)

## Step 4 : Bundling

Open the file `ProjectNameWebModule.cs` in `web` project and add the following code inside `ConfigureBundles` functions like below.

```
Configure<AbpBundlingOptions>(options =>
            {
                options.StyleBundles.Configure(
                    StislaThemeBundles.Styles.Global,
                    bundle =>
                    {
                        bundle.AddFiles("/libs/fullcalendar/fullcalendar.css");
                    }
                );
            });

            Configure<AbpBundlingOptions>(options =>
            {
                options.ScriptBundles.Configure(
                    StislaThemeBundles.Styles.Global,
                    bundle =>
                    {
                        bundle.AddFiles("/libs/fullcalendar/fullcalendar.js");
                    }
                );
            });
```

## Step 5 : Result

![lib path](../_images/frontend/calendarresult.png)
