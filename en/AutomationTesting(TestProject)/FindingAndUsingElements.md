# Finding and Using Elements

TestProject provides various ways of finding and accessing elements.  You can use the [Element Locator](http://192.168.0.248:44612/documents/en/abp/latest/AutomationTesting(TestProject)/FindingAndUsingElements#element-locator), [Element Inspector](http://192.168.0.248:44612/documents/en/abp/latest/AutomationTesting(TestProject)/FindingAndUsingElements#element-inspector) to find the elements that you need to interact with.

## Element Locator

It allows you to try out element locators in a dynamic way. 

For example, you can easily test your xPath against the DOM to find the most suitable expression.

![Alt text](../AutomationTesting(TestProject)/_images/ElementLocator.png)


This will open the element locator.  
You can then test out xPath, ID or name locators to find a suitable expression. 
To test out a locator, just enter it into the text box and hit the evaluate button. If it is a valid path that represents an element on the screen, TestProject will highlight it for you so that you can see how your selector is working.

![Alt text](../AutomationTesting(TestProject)/_images/LocatorEditor.png)

You can use this tool to create better, and more generic xPath expressions to be used in your recorded or coded tests. Remember, better xPath locators make your tests more stable and reduces the need in maintenance.

## Element Inspector

- It gives you instant access to the element attributes, a huge variety of actions and a list of validation options. 
- You can activate the element inspector by mousing over an element and then either hitting shift twice quickly, which is known as double shift, or by clicking on the 3 vertical dots on the tool tip.

![Alt text](../AutomationTesting(TestProject)/_images/FreezeElement.png)

- Doing this will freeze the element and give you access to all the element inspector menu options.  

- You can   perform various actions on the element through the Actions menu, or you can take a look at the elements attributes with the Attributes menu, or if you like, you can create validations against the element with options in the Validations menu.

![Alt text](../AutomationTesting(TestProject)/_images/InspectorOptions.png)