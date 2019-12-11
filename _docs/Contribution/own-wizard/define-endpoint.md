# Creating your Recipe Execution Endpoint

!!! note ""
    - This applies to Recipe Implementor.
    - This section explains the process for creating a Recipe wizard.
    - This endpoint can be defined in cockpit javascript file only as of now!


**Recipes** requires an execution endpoint where the recipe executes using a payload formed by the client like wizard. The endpoint is also responsible for updating recipe execution progress.

## *Creating an Endpoint*
A recipe execution endpoint needs to define a execute method as below:
``` json
sap.ui.define(
	[
		"cockpit/recipe/core/common/TaskExecutor",
		"cockpit/recipe/core/common/ConfigReader",
		"cockpit/recipe/account/onboarding/TaskPayloadAdapter",
		"cockpit/recipe/cf/public/tasks/Tasks",
		"cockpit/recipe/cis/public/tasks/Tasks"
	],
	function(taskExecutor, configReader, taskPayloadAdapter, cfTasks, cisTasks) {
        execute : function execute(allStepCommonInfo, wizardAllStepData, progressService) {
            //Do Work
        }
    });
```
`allStepCommonInfo`: This contains common information apart from the data coming from wizard steps, which your recipe needs for execution. You can see below an example of kind of data you can provide via this
``` json
{
  accountID: <accountID>,
  recipeID: <recipeID>,
  environmentType: <environmentType>,
  isGlobalAccountAdmin: true|false,
  prerequisitechecks: []
}
```

`wizardAllStepData`: This contains all the user input data provided by your wizard steps. The input data is stored against the page(View id you gave in recipe provider) id in this method argument.
`progressService`: This reports progress to the client.


There are 2 ways of designing an endpoint:

1. Task based endpoint (Recommended).
2. Free Style endpoint.

### *Task based endpoint*
The task based endpoint provides you a structural way to write your endpoint. Writing this type of endpoint makes managing your tasks simpler. Task based endpoint needs you to define:

* Task List: List of tasks, in the execution order. A task is an atomic Step in recipe execution. A recipe needs to define all tasks required for execution. Below is code snippet for task:
``` json
{
    id: "Unique Id",
    title: "My Recipe Task01",
    execute: function(payload){},
    rollBack: function(executedData){}
}
```

* Task Payload Adapter: This helps to adapt data from one task to the next task. Below is code snippet for Task payload Adapter:
  ``` json
  {
    setInputData : function(allStepCommonInfo, wizardAllStepData) {},
    updateExecutedStepData : function(taskId, createdData),
    getPayloadForTask : function(taskId),
    getAllExecutedTasksData: function()
  }
  ```
`setInputData`: Sets the initial configuration data and all the wizard step data.
`updateExecutedStepData`: Stores the response data of the task executed against the task id.
`getPayloadForTask`: Converts the data provided by the endpoint in the format which can be understood by the task executer.
`getAllExecutedTaskData`: Returns the response of all the executed tasks.

Please refer to our Task Payload Adapter(link) to have a better understanding.


Recipe SDK comes with Task Executor which executes a list of task in a sequential order.
``` json
sap.ui.define([ ],
  function() {
      execute : function(tasks, taskPayloadAdapter, progressService) {}
   });
```

### *Free Style endpoint*
A free style endpoint, as the name suggests, will set you free from all kinds of restrictions of the task based endpoint. This type of endpoint is recommended when your recipe has goals which requires very less computation(one or two tasks in terms of tasks), otherwise this way may make your code a bit messy.