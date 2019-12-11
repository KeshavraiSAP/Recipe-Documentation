# Creating your Wizard

!!! note ""
	- This applies to Recipe Implementor.
	- This section explains the process for creating a Recipe wizard.

**Recipes** works as a setup assistant, that is a wizard, which offers sequential steps to reach specific, clear goals. These steps collect all the necessary information needed to run the recipe. Each step in a wizard has a view and a controller corresponding to that step.

## _Selecting a module_

If you are already <a href="https://wiki.wdf.sap.corp/wiki/display/ADI/Cockpit+contributors+and+the+contribution+process" target="_blank">cockpit contributor</a>, then you can do all these changes in your module.
If you don't own a module in cockpit, you can be part of the cockpit-module-recipe module in cockpit.

## _Adding a Wizard Metadata Provider_

Wizard Metadata provider is responsible for providing all the required information to build a Wizard UI Components. Recipe Implementors needs to provide below information to the framework for building the wizard:

- Pages : This is list of pages to be shown in the wizard.
- Prerequisite Checks : This is list of prerequisite checks to be run.
- Finish Dialog : The final dialog to be shown after the recipe run.

```json
sap.ui.define([
    "cockpit/recipe/core/wizard/BaseRecipeProvider",
    "cockpit/recipe/core/common/DefaultChecks"
],
  function(BaseRecipeProvider, DefaultChecks) {
    return BaseRecipeProvider.extend("cockpit.recipe.demo.blueprintRecipeProvider", {
       getPages: function(initialData) {
			return [
				{
					id: "blueprint_Step1",
					name: "Hello World", //Needs to be localized
					viewName: "cockpit.recipe.demo.public.pages.blueprintPage01",
					subTitle: "Hello World",
					hint: "This is sample hello world(public)"
				},
				{
					id: "blueprint_Step1",
					name: "Hello World Again", //Needs to be localized
					viewName: "cockpit.recipe.demo.private.pages.blueprintPage02",
					subTitle: "Hello World Again",
					hint: "This is sample hello world(private)"
				}
			];
		},
		getPrerequisiteChecks: function(initialData) {
				return [DefaultChecks.globalAccountAdminCheck];
		},
        getFinishDialogFragment: function(wizardAllStepData, allTaskResolvedData) {
          var i18nModel = this.getResourceModel("cockpit.recipe.demo");
          var finishFragment = sap.ui.xmlfragment("cockpit.recipe.demo.fragments.FinishDialog", this);
          finishFragment.setModel(i18nModel, "i18n");
          return {content: finishFragment, type:"success"};
        }
    });
  }
);

```

- `BaseRecipeProvider`: Abstract implementation of the <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/js/cockpit/recipe/core/wizard/BaseRecipeProvider.js" target="_blank">Recipe metadata provider</a>. Your provider file needs to extend a base provider file (BaseRecipeProvider.js) provided by the framework.
- `DefaultChecks`: This is place where all the mostly used prerequisite checks reside. <a href=https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/js/cockpit/recipe/core/common/DefaultChecks.js target="_blank">DefaultChecks</a> provides you with some of the commonly used checks which you can reuse for your recipe.
- `getPages`: This method is required for the provider to provide views for the recipe. This must return the pages which will be part of the recipe Wizard. You have to mention the path of the corresponding view which you want in your wizard. Each page is represented as :
  ```json
  {
    "id": "blueprint_Step1",
    "name": "Hello World", //Needs to be localized
    "viewName": "cockpit.recipe.demo.public.pages.blueprintPage01",
    "subTitle": "Hello World",
    "hint": "This is sample hello world(public)"
  }
  ```
  
  - `getPrerequisiteChecks`: This method is required for the provider to provide prerequisite checks for the recipe. This method returns the prerequisite checks to be run as part of recipe execution. Prerequisite check runner screen is auto injected when there is at least 1 check returned via this method. See [here](define-wizard.md#creating-a-prerequisite-check) to know how can you create a prerequisite check.

* `getFinishDialogFragment`: This method is required to provide content & type for the finish dialog which will be shown on successful execution of recipe. 
	- `content` - Xml fragment.
	- `type` - Possible values are "success", "warning". Optional parameter with default value as success.

## _Creating a Wizard page_

This section explains how you can create a new page which can be added to the Recipe Wizard.

Each page controller must extend from <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/js/cockpit/recipe/core/wizard/BaseWizardPageController.js" target="_blank">Base Wizard Page Controller</a>

```json
sap.ui.define(
  [
    "cockpit/recipe/core/wizard/BaseWizardPageController"
  ],
  function(BaseWizardPageController) {
    return BaseWizardPageController.extend("cockpit.recipe.demo.public.pages.blueprintPage01", {});
  });
```

As your controller is extending from the base wizard controller, there are some methods already provided by the base controller which you can use to handle your wizard, populate your pages and expose your step data to be used later by the executer and summary page.

- `onAfterShow` : In most cases, child page controller will need to implement this function. It will be a good place where API calls are made inside the child page controller and update the view model data. This function will be called every time when step page is a navigated.

!!! note ""
	- Please be noted that if you don't want a logic to be called every time step is navigated, you can cache the values.
	- Cockpit does support caching api values in BackendUtil.getJson method.

- `getInitialData`: The initial data from the outside world, which will be passed in the client code when it brings up the wizard dialog.
- `getData` : This method returns the view data of the current page. If child page stores the data with a named view model, it should override this function.
- `getOverallData`: This is the way how one step page controller can read data from previous page(s), the data returned from this function will have the following structure:

```json
{
'page_id_1': <view model data from the default view model of the page identified by the page id 'page_id_1'>,
'page_id_2': <view model data from the default view model of the page identified by the page id 'page_id_2', actually, it's the same as the return data of 'getData' of the page controller'
}
```

!!! note ""
	- 'page_id_1' and 'page_id_2' will be the same as used in the client code, the 'id' property value of the 'PageDefinition'.
	- If one step page needs to use the data from a previous step, and you want to reuse these dependant pages in another wizard, please make sure the step IDs are the same in all the wizard client code; And this case also means these pages are tightly coupled together, but the coupling is also outside the wizard framework.

- `notifyValidationStatus`: This method notifies the wizard for enabling or disabling the wizard "Next" option. To make things simple, this framework needs each page controller notify the validation status, either true(valid) or false(invalid), by calling this function. You can call this initially with false value to disable Next option.
- `notifyBusinessModelPropertyChange`: User can navigate to previous steps using the top Step numbers as well. If user navigates using the Step numbers and modifies the data, then in some cases, framework needs to disable the future steps. This can be achieved using this method.
- `onInit`: If you are overriding this method and using a named model, Please add _BaseController.prototype.onInit.apply(this,arguments)_ as first line in this method.
- `getSummaryData`: The method returns summary data which will be shown in the 'review' step of your wizard. It follows a certain structure as stated below:

  ```json
  {
    title: <localized string, as the main group title>
    subtitle: <localized string, as the sub title, optional>
    properties: [
                {
                  name: <localized string as the property name>
                  value: <string>
                }
                ]
  }
  ```

Page can be designed for reusability and consumed in other recipes. We recommend to keep such pages in folder structure like "blueprint\public\pages\myPublicPage.controller.js". For example : <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/js/cockpit/recipe/cis/public/pages/SetupSubaccount.controller.js" target="_blank">CIS SetupSubaccountController</a>

## _Creating a prerequisite check_

This section explains how you can create a prerequisite check. These checks run at the start of recipe.

```json
{
  "title": getResourceBundle().getText("BLUEPRINT_CHECK_XTIT"),
  "description": __getResourceBundle().getText("BLUEPRINT_CHECK_XTIT"),
  "execute": function(recipeId, accountId, starterInfo) {
    var executionPromise = jQuery.Deferred();
    var isCheckSuccessful = doCheck();
    if (isCheckSuccessful) {
      executionPromise.resolve(true);
    } else {
      executionPromise.reject({ "title": "", "errorText": "", "solution": "" });
    }
    return executionPromise;
  }
}
```

- `title`: This field represents the prerequisite check title.
- `description`: This field represents the prerequisite description shown in info icon.
- `execute`: This method executes the check and returns a promise. The promise can be resolved to a 'true' value which means the check passed. The promise can be rejected with a error object as shown in above example.

## _Define wizard configuration(Not Mandatory)_

Recipe might require certain external configuration for running. For example, an recipe might require a set of minimum entitlements defined. This can be configured in <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/resources/META-INF/resources/cockpit/recipe/config.json" target="_blank">config.json</a>
