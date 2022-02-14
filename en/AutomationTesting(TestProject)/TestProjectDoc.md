# Creating a Web Test using the TestProject Recorder
## Create Project & Add Test Steps
- `step 1 : When you first start using TestProject, it will prompt you to create a new project.` 

    ![Alt text](../AutomationTesting(TestProject)/_images/CreateProject.png)

- `step 2 : Once you give your project a name and description and create it, TestProject will take you to the tests page for that project.`

- `step 3 : Creating a Test`
    
    - Creating a web test is as simple as clicking on the New Test button and then choosing the Web option as your test type
    
        ![Alt text](../AutomationTesting(TestProject)/_images/NewTest.png)

    - To Create a Web test, so choose that option and then click on Next.

        ![Alt text](../AutomationTesting(TestProject)/_images/ChooseWeb.png)

    - You can now give your test a name and description. You can also add tags to your test to help organize tests so that you can more easily search and filter them in the future.

        ![Alt text](../AutomationTesting(TestProject)/_images/TestDetails.png)

    - The next thing you will be asked for is the application you are going to test. If this is the first test you are creating there will not be any applications in the list and so you will need to click on the Add a new application for testing button

        ![Alt text](../AutomationTesting(TestProject)/_images/ChooseApplication.png)

    - This will prompt you for the URL of the application that you want to test and also ask you to give it a name for the system to use. For this example we will put in the URL of the TestProject example site.

        ![Alt text](../AutomationTesting(TestProject)/_images/AddWebsite.png)

- `step 4 : Recording a Test`
    
> In order to use the recorder you will need to make sure that you have a local agent setup and running.  If you have not yet setup and started an agent you can follow the instructions in the [installation and setup](../AutomationTesting(TestProject)/AgentSetup.md) section to do so.

- If the `Start recording` button is not available after you click on the Record icon, be sure to check that your agent is running and connected.

    ![Alt text](../AutomationTesting(TestProject)/_images/RecordTestDisabled.png)

    - Once a local agent is running you will be able to click on the record button andhave the option to click on the Start recording option.  It should look something like this:

        ![Alt text](../AutomationTesting(TestProject)/_images/RecordTestEnabled.png)

        Choose to save the recording of the test steps in the cloud

- `step 5 - Add Test Steps`

    - Simply click a step is automatically added to the test

        ![Alt text](../AutomationTesting(TestProject)/_images/RecordStep.png)

## Adding Validation

-  To do this you can mouse over the name on the page and then hit shift twice quickly (Double Shift) to freeze the element.

    ![Alt text](../AutomationTesting(TestProject)/_images/SelectValidation.png)

- Doing this will bring up a menu with a few options. Since you are trying to validate something, you will choose the Validations option. Just mouse over that to bring up the available options.

    ![Alt text](../AutomationTesting(TestProject)/_images/SelectValidationSteps.png)

    This will open the create a new step panel where you can type in the text that you expect to see in this element.

- You can then click on the Save Step button to add this test step to your test. 

    ![Alt text](../AutomationTesting(TestProject)/_images/SaveValidation.png)

## Using Data Driven Tests and Jobs in TestProject (Adding Parameter or Multiple Parameters)

### Parameterize Inputs
- Once you have a test setup, the first thing you will need to do is to parameterize the variable that you want to use to drive the test. 
- To do that open the test in the test editor by clicking on it in the project view. You can then open the parameters panel for that test from the more menu.

    ![Alt text](../AutomationTesting(TestProject)/_images/Parameters.png)


- You can then add a new parameter with the plus button at the bottom of the panel.

    ![Alt text](../AutomationTesting(TestProject)/_images/NewParameter.png)

- Fill in the name, description and value for the parameter and click on the add button to add it to your test.

    ![Alt text](../AutomationTesting(TestProject)/_images/SaveParameter.png)

    This parameter is now ready to be used in the test. 

- Navigate to the test step where you want to use it and click on that step in the step editor. Find the Input parameters section on the editing panel and click on the plus beside the input parameter you are interested in.

    ![Alt text](../AutomationTesting(TestProject)/_images/Inputparameterssection.png)

- Clear any text already entered and then choose the parameter you created from the list of parameters.

    ![Alt text](../AutomationTesting(TestProject)/_images/ChooseParameters.png)

    This will add the parameter as a variable in the input field and you can click on the check mark at the bottom of the panel to apply the change.

- Click on the Save button on the test step panel and you will now have your input field parameterized.

    ![Alt text](../AutomationTesting(TestProject)/_images/SaveParameterInTestStep.png)

## Generating a Data Source
 
> When testing the same application set the Parameter ApplicationURL to Private like in the image below before generating a Template. Setting a Parameter to private will hide it when providing a Data Source. do the same for every parameter you are not planning to override with the data source.

![Alt text](../AutomationTesting(TestProject)/_images/PrivateParameter.png)

In order to run a data driven test, you will need to create the data that you want to use and add it to the system. 

- Go to the test you want to generate data for and use the more menu on it to download a CSV Parameters Template. 

    ![Alt text](../AutomationTesting(TestProject)/_images/CSVTemp.png)

    This will download a .csv file to your computer with headers for each of the parameters you have defined in your test. 

- You can open the file in Excel or any other spreadsheet editing program and add in rows for each input you want to add.

    ![Alt text](../AutomationTesting(TestProject)/_images/ExcelParam.png)

- Once you have added the data that you want you can save the file as a .csv file again and upload it back into the TestProject app. To do that, go to the Data Sources menu option and choose to Add a data file.

    ![Alt text](../AutomationTesting(TestProject)/_images/SaveDataSource.png)

    Choose the .csv file you created and give it a name on click on Create. This will add the file to the system as an input source that you can use.

### Running Tests with a Data Source

Now that there is a data source in your project you can use it to drive your test. 

- Click on Run icon for the test and choose the agent and platform that you will run the test on.

    ![Alt text](../AutomationTesting(TestProject)/_images/RunTest.png)

- On the next page , choose Browser and Agent.
- On the next page, you will be prompted for fill in the parameters for that test. To run a data driven test, enable the Override default input parameters option.

    ![Alt text](../AutomationTesting(TestProject)/_images/UseDataSource.png)

    You can then select the data source you previously added and click on run.  The test will run once for each row that you have added in the .csv file using the input for that row.
