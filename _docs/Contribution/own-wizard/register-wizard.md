# Register your Wizard

You are almost there!... Just 2 steps away

## Register your endpoint
Endpoint needs to be registered in Wizard Metadata Provider. You need to add a method like below:

``` json
getEndpoint: function() {
  return Endpoint;
}
```

## Register your wizard
Now register the Wizard metadata provider in the <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/master/cockpit-module-recipe/src/main/js/cockpit/recipe/core/wizard/BaseWizardPageController.js" target="_blank">registration.json</a>

``` json
{
	"recipeGuid": "cockpit.recipe.bluesprint.DemoRecipeProvider"
}
```

`recipeGuid`: Guid of Recipe from <a href="https://git.wdf.sap.corp/plugins/gitiles/NGJP/JPaaS/com.sap.core.account/+/0b237823137a630250c0b27896a742b84ccd6c2f/cockpit-module-recipe/src/main/resources/META-INF/resources/cockpit/recipe/recipemanager/cockpit/cockpitRecipeInfo.json" target="_blank">recipe documentation.</a>
`cockpit.recipe.bluesprint.DemoRecipeProvider` : Path of the Recipe  metadata provider


Now you should see the Start Wizard option!