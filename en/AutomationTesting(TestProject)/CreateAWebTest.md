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

