# Steps Widget

A test in TestProject is composed of a number of tests steps. These steps can be automatically added to the test when working with the test recorder, or they can be manually added using the Add Step plus icon at the bottom of the steps widget. In either case, the steps widget can be accessed by clicking on the test step of interest.

## Test Step Widget

There are 5 main parts to a test step, as shown in the following image.

![Alt text](../AutomationTesting(TestProject)/_images/StepWidget.png)

### Comments
Comments about the test step can be entered in section one. This can be helpful for clarifying what the intention of the test step is, or can be a place to leave a message for teammates with information that might help when running this step in the future.

### Action
The second part of a test step is the action that the step will perform. 

- This can range from clicking on an element to typing in text to element validation and much, much more. 
- If you click on the action cell in a test step you can search through all the available actions to find the one that you want. 
- The full list of actions is can also be seen on the [Available Actions](https://docs.testproject.io/using-the-smart-test-recorder/available-actions). 
- Validations are also considered to be actions and you can see the full list of them on the [Available Validations](https://docs.testproject.io/using-the-smart-test-recorder/available-validations).

### Element
This is where you define what element is being acted on. 

When using the smart recorder, the element locator will be automatically filled in. If you are creating a step manually, you can click on this field to search for the element. 

- For example, if you wanted to find the password element on a page, you could type `pass` into the search bar, and it would find any elements that matched that search.  

    ![Alt text](../AutomationTesting(TestProject)/_images/Eg_PasswordElements.png)

- You can also create your own element if you need to use a custom selector that does not show up in the list. Additionally, you can edit elements by using the Edit Element icon.

    ![Alt text](../AutomationTesting(TestProject)/_images/Eg_EditElement.png)

- You can also add your own locators in here as well if you know of one that will be stable. An example of what this looks like for the password field on the TestProject example page is shown in the following image:

    ![Alt text](../AutomationTesting(TestProject)/_images/Eg_ElementSelectorsforPasswordField.png)

    You can see that it will first try to use the CSS Selector, but if that doesn't work it will look at a few different XPATHs to hopefully find the element for you.

> One other item worth noticing about the element field on a test step is that you can click on the Find Element magnifying glass and TestProject will highlight where on the page the element is.

![Alt text](../AutomationTesting(TestProject)/_images/Eg_FindElementonPage.png)

This is very helpful to check that the locator is working correctly and to see what exactly is going on.

### Input
The fourth section on the step panel is optional, depending on the kind of action you are doing. 
Some actions like `click` do not require any inputs, while others like `type text` require you to enter the text that you want to be typed. The input fields will automatically appear depending, on the action selected.

## Advanced Options

There are a number of options in here that allow you to control various aspects of how a step works.

### On Failure Behaviour

This option allows you to specify what you want TestProject to do if the test step fails.

- You can choose to `Continue Test` if you want TestProject to run the remaining test steps even if this one fails. You might want to do this if you have some cleanup steps that you want to run for example.
- You can, of course, choose the `Fail Test` option, which will fail the test and stop running it if the test step encounters an issue.
- The `Always Pass` option will make it so that any failures on the test step don't cause the test to fail. You will probably not use this option very often, but there may be times when you need to run something in a test step that you know will fail, and you don't want to cause the test to fail.

## Take Screenshot

TestProject can take screenshots of what is happening in your tests. 

- This is really helpful when you are trying to debug failing tests and so this option defaults to `On Failure` which means that TestProject will take a screenshot of the application any time a test step fails. 
- You can turn of taking screenshots completely by using the `Never` option, or you go to the other extreme and take a screenshot after each test step by choosing the `Always` option. 
- The final choice is to take a screen when the test step passes but not when it fails. You can do this with the `On Success` option.

