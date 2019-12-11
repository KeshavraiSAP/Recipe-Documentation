# Implementing Flex Based wizard Step.

!!! note ""
    - The below example view can also be used as a prototype for creating your own views.
    - The controller inherits functionalities which have been abstracted to its container controller which provides certain utils and is explained in the [Controller](./implementing-view.md#controller-hooks) section below.

## The View

We create the view with some simple content:

```bash
touch cockpit-module-recipe/src/main/js/cockpit/recipe/explore/FirstPage.view.xml
```

#### Example view

```xml
<mvc:View xmlns="sap.m"
          xmlns:mvc="sap.ui.core.mvc"
          controllerName="cockpit.recipe.explore.FirstPage"
          height="100%">

    <VBox>
        <Title text="first block control" class="sapUiLargeMarginBottom"/>
        <Title text="If this was a real page" class="sapUiLargeMarginTop sapUiLargeMarginBottom"/>
        <Title text="you would need to" class="sapUiLargeMarginTop sapUiLargeMarginBottom"/>
        <Title text="use proper i18n placeholders" class="sapUiLargeMarginTop sapUiLargeMarginBottom"/>
        <Title text="instead of using plain text" class="sapUiLargeMarginTop sapUiLargeMarginBottom"/>
    </VBox>

</mvc:View>
```

## The Controller

In the view we have declared a controller name `cockpit.recipe.explore.FirstPage`.
Let's create it:

```bash
touch cockpit-module-recipe/src/main/js/cockpit/recipe/explore/FirstPage.controller.js
```

#### Example controller

```js
sap.ui.define(
	[
		"sap/ui/core/mvc/Controller",
        "sap/ui/model/json/JSONModel",
        "cockpit/project/common/shared/api/client",
        "cockpit/project/common/projectConstants",
        "cockpit/project/common/projectUtil",
	],
    function(
            Controller,
            JSONModel,
            client, // single point for making Ajax Calls
            projectConstants, // all constants being used across project are maintained here
            projectUtil, // utility file for project
            ) {
		"use strict";

		return Controller.extend("cockpit.recipe.explore.FirstPage", {
			onInit: function() {
                /* parentObject:
                *  util methods shared from the container controller of this page.
                *  use this only if the parent supports sharing his object.
                */
                this.parentObject = this.getView().getViewData().getParentObject();
 				var oResourceModel =  new sap.ui.model.resource.ResourceModel({
					bundle : projectUtil.getResourceBundle()
				});
                this.getView().setModel(oResourceModel, "i18n");
                this.oModel = new JSONModel({
					someProperty: 200
                });
                this.getView().setModel(this.oModel);

			},

			onExit: function() {
				// ...
			},

			/**
			 * project hook (required), is invoked everytime
			 * when user navigates to this page. page is kept
			 * busy until this promise is resolved: used
			 * to load data for the view from your backend.
			 * @param context
			 * @returns {jQuery.promise}
			 */
			updateDataForView: function(contextObject) {
                this.__resetModel();
				return this.__populateData();
            },

            __resetModel: function() {
				// ...
            },

            __populateData: function() {
                // ...
                return jQuery
					.Deferred()
					.resolve()
					.promise();
            }
		});
	}
);
```

### Controller hooks

Hooks are functions that you implement on the controller associated with the page's view. They are automatically invoked by the framework. In addition to the regular hooks provided by SAPUI5, such as <a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller/methods/onInit" target="_blank">onInit</a> or <a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller/methods/onExit" target="_blank">onExit</a>, the framework provides custom hooks.

Noteworthy hooks are:

- **Update Data for View** (required)

    The `updateDataForView` hook is passed a `contextObject` when ever the page is accessed.

    This object provides a context for the update of view-model each time the page is accessed.



### View Data

Each view if supported, is passed a `parentObject` during view creation by its container view or parent view. This is a `sharedObject` being passed to all its siblings as well and this object provides a number of essential functions for (among others):

  - navigation to other tabs
  - accessing the current scope or it's parents.
  - get user permissions for a project.

!!! note "Note:"
    - These methods may vary as they depend on the container view.