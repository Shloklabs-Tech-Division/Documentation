# Buttons
## Introduction
`abp-button` is the main element to create buttons.

### Basic usage:
```XML
<abp-button button-type="Primary">Click Me</abp-button>
```

## Attributes
### button-type
A value indicates the main style/type of the button. Should be one of the following values:

- Default (default value)
- Primary
- Secondary
- Success
- Danger
- Warning
- Info
- Light
- Dark
- Outline_Primary
- Outline_Secondary
- Outline_Success
- Outline_Danger
- Outline_Warning
- Outline_Info
- Outline_Light
- Outline_Dark
- Link

## size
A value indicates the size of the button. Should be one of the following values:

- Default (default value)
- Small
- Medium
- Large
- Block
- Block_Small
- Block_Medium
- Block_Large

## busy-text
A text that is shown when the button is busy.

## text
The text of the button. This is a shortcut if you simply want to set a text to the button. 

Example:

```XML
<abp-button button-type="Primary" text="Click Me" />
```
In this case, you can use a self-closing tag to make it shorter.

## icon
Used to set an icon for the button. It works with the Font Awesome icon classes by default. 

Example:
```XML
<abp-button icon="address-card" text="Address" />
```
## icon-type
If you don't want to use font-awesome, you have two options:

1. Set `icon-type` to `Other` and write the CSS class of the font icon you're using.
2. If you don't use a font icon use the opening and closing tags manually and write any code inside the tags.

## disabled
Set true to make the button initially disabled.


# **Samples** :
## Add Button in cards

### "New sprint" Button
Consider the example from `Pages/Sprints/Index.cshtml` and set the content of `abp-card-header` tag as below:


```html
<abp-button id="NewSprintButton"
            text="@L["CreateSprint"].Value"
            icon="plus"
            button-type="Primary" />           
```

The above code renders such button:

![Alt text](\_images\CreateSprintButton.png)


## Buttons in `Create.cshtml` of `Sprint` entity Create form (Popup forms):


![Alt text](\_images\CreateNewSprint.png)

Code for this form is :
```html
@page
@using Microsoft.AspNetCore.Mvc.Localization
@using Volo.Abp.AspNetCore.Mvc.UI.Bootstrap.TagHelpers.Modal;
@using SHLOKERP.Localization
@inject IHtmlLocalizer<SHLOKERPResource> L
@model SHLOKERP.Web.Pages.Sprints.Sprint.CreateModalModel
@{
    Layout = null;
}
<abp-dynamic-form abp-model="ViewModel" data-ajaxForm="true" asp-page="CreateModal">
    <abp-modal>
        <abp-modal-header title="@L["CreateSprint"].Value"></abp-modal-header>
        <abp-modal-body>
            <abp-form-content />
        </abp-modal-body>
        <abp-modal-footer buttons="@(AbpModalButtons.Cancel|AbpModalButtons.Save)"></abp-modal-footer>
    </abp-modal>
</abp-dynamic-form>
```

* `abp-dynamic-form` tag helper completely automates the form creation.
* ### ABP Form Tag Helpers

    `abp-dynamic-form` covers most of the scenarios and allows you to control and customize the form using the attributes.

    However, if you want to render the form body yourself (for example, you may want to fully control the form layout), you can directly use the `ABP Form Tag Helpers`.

    ```html
        <abp-modal-footer buttons="@(AbpModalButtons.Cancel|AbpModalButtons.Save)"></abp-modal-footer>
    ```
 