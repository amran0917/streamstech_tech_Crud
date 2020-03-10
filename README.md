JantrikCrud!
===================
JantrikCrud is a library for creating a *form* of data insertion and update through some configuration. It also allows to build a data table from the configuration. The data table has options for editing and deleting a data.
________________
Quick Start
-------------
Here lies the setup of a new project. Instruction for installing JantrikCrud. Installing dependencies (in case of copy-paste; a bad thing). Configuring server side and referring necessary files in client side and... Implementation.
### Installation
`nuget install Jantrik.Crud`
### Configuration
#### Server Side
1. Enable attribute routing. Add the code segment mentioned below in your `RouteConfig.cs` file. 
```
routes.MapMvcAttributeRoutes();
```
2. Mention the assembly name of the project containing the database models of your application in `UnityConfig.cs` file inside `App_Start` folder.*
```
Assembly.Load("AssemblyName")
```
3. Modify the database connection string in your `WebConfig`.**

> ***Tip**: The assembly name can be found in the properties of the project.
>****Note**:  If you use a different `name` of the `connectionString`, change the database instantiation in `UnityConfig.cs` accordingly.

#### Client Side
1. Load dependent styles and scripts.
```
<link href="bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">
<link href="handsontable/dist/handsontable.full.min.css" rel="stylesheet">
```
```
<script src="angular/angular.min.js"></script>
<script src="angular-route/angular-route.min.js"></script>
<script src="jquery/dist/jquery.min.js"></script>
<script src="bootstrap/dist/js/bootstrap.min.js"></script>
<script src="angular-bootstrap/ui-bootstrap.min.js"></script>
<script src="angular-bootstrap/ui-bootstrap-tpls.min.js"></script>
<script src="handsontable/dist/handsontable.full.min.js"></script>
<script src="checklist-model/checklist-model.js"></script>
```
2.  Load directive specific styles and scripts.
```
<link href="property-table/property-table.css" rel="stylesheet">
```
```
<script src="model-table-module.js"></script>
<script src="property-table-module.js"></script>
```
###Implementation
#### Server Side
1. Make a configuration collection named `DbModelConfig` in the database.
2. Create model classes for the database collections of your server application.*
3. Configure the models of your application in the `DbModelConfig` collection. Each `Documents` contain the configuration of a single model.**
>***Note**: Model class names need to be matched with the collection names in your database (case sensitive).
>****Tip**: Example configuration and configuration options can be found in _Model Configuration in Database_ section below.
#### Client Side
1. Inject dependent modules in your app module.
```
var app = angular.module('app', ['jantrik.modelTable', 'jantrik.propertyTable'])
```
2. Load directives. 

Load 
```
<crud-model-table></crud-model-table>
```
to show list of data. And load
```
<crud-property-table></crud-property-table>
```
to show the create/edit form.
>**Note**: The model name is resolved either from a directive attribute *modelName* or from the URL. To learn more read the *Directives* section.
Dependencies
-------------
#### Server Side Package Names
 1. Jantrik.DAL.Mongo1
 2. mongocsharpdriver
 3. Newtonsoft.Json
 4. Unity
#### Client Side Package Names
1. angular
2. angular-bootstrap
3. angular-route
4. bootstrap
5. checklist-model
6. handsontable
7. jquery*
8. moment*
9. pikaday*
10. zeroclipboard*
>***Note**: Installed as dependency of other libraries.
Model Configuration in Database
-------------
This is the document structure of `DbModelConfig` collection in your database. There are two fields in the document: Name and Config.

#####**Name** `string`
Name of the database collection. This is the name you want to be the name of your collection in the database.
#####**Config** `Array of object`
Configuration of a model class in the server application. Each object in the Config array is the configuration of a property of the model.

**Example**
```
{
    "Name" : "User",
    "Config" : [ 
        {
            "Name" : "Id",
            "Type" : "hidden",
            "Hidden" : true
        }, 
        {
            "Name" : "FirstName",
            "Label" : "First Name",
            "GroupName" : "Name",
            "Col" : 6
        }, 
        {
            "Name" : "LastName",
            "Label" : "Last Name",
            "GroupName" : "Name",
            "Col" : 6
        }, 
        {
            "Name" : "EmailAddress",
            "Label" : "Email Address"
        }, 
        {
            "Name" : "RoleId",
            "Label" : "Role",
            "Type" : "dropdown",
            "OptionSource" : "http://localhost:4267/Role"
        },
        {
            "Name" : "IsRegular",
            "Label" : "Regular",
            "Type" : "checkbox",
            "Hidden" : true
        }
    ]
}
```
Here, We configured a collection named `User`. We configured five fields of this collection named: `Id`, `FirstName`, `LastName`, `EmailAddress` and `Role`. The display text for all these fields are configured through `Label` option.

The input type of `Id` field is `hidden`. We don't want any user to input the value of this field, the value will be automatically generated from server. The `Hidden` option in `Id` field hides it from the model-table view.

`FirstName` and `LastName` are grouped together using the `GroupName` option. Their size in the view is also modified with the `Col` option. It will span through 6 grids of bootstrap grid system.

The input type of the `Role` field is dropdown. The options of the dropdown will be loaded from the URL provided through the `OptionSource` field value.

`IsRegular` is a boolean field and the input is taken from a checkbox.

Fields which are not given any input type are by default `text` type.
> **Note**:  Fields that are not configured will not be processed.
### Config Options
#####**Name** `required` `string`
Name of the property of a model. This name is used to map the configuration with the property. This name and the property name in the model class need to be same.
#####**Label** `string`
Display text of the property name in the form

Default value:  value of the `Name` field
#####**GroupName** `string`
Properties with same GroupName are grouped together. Properties of one group are shown inside a `well` (bootstrap).

Default value: "Default"
#####**Type** `string`
Type of input of the property in the form. 
| Value | Description |
| - | - |
|`'chechbox'`| allows boolean input |
|`'checklist'`| allows list of inputs from a list of options |
|`'dropdown'`| allows input only from the dropdown list |
|`'editableDropdown'`| allows input either from the dropdown list or `'text'` input from user |
|`'hidden'`| input is not allowed for this property |
|`'radio'`| allows input from the list of radio options |
|`'text'`| allows plain text input |
|`'textarea'`| allows plain text input in a text area |
Default value: `'text'`

> **Tip**: To have a custom type, write the type name (e.g. `button`) in the configuration and make a directive for the type. Later, register the directive for this type before loading the directive.
> ```
> app.module('exampleApp', ['jantrik.propertyTable']).
> 
> app.controller('upsertCtrl', ['propertyDirectiveRegistry',
>     function(directiveRegistry){
>         directiveRegistry.register('button', 'custom-button');
>     }
> ])
> ```
#####**OptionSource** `string`
OptionSource accepts URL as value. The URL loads data for the `checklist`, `dropdown`, `radio` and `editableDropdown` types.

Format of the data from URL for `checklist`, `dropdown` and `radio`:
```
[
	...
	{
		"value": "hidden value",
		"label": "text to display"
	}
	...
]
```
Format of the data from URL for `editableDropdown`:
```
[ "value1", "value2", ...]
```
>**Note**: This option is `required` for `checklist`, `dropdown`, `editableDropdown` and `radio` types
#####**Hidden** `boolean`
Determines whether the property will be displayed in the list view. `true` value hides the property from list view.
 
Default value: `false`
##### **Readonly** `boolean`
Determines whether the field is readonly (non-editable input field). 

Default value: `false`
##### **Col** `int`
`col` size according to bootstrap 3. This value defines how much space the input shuold take in the grid system of bootstrap.

Default value: 12
Directives
-------------
___
#####**crud-create-model**
Can be added only by element.
#####attributes: 
| Name | Type | Description |
| - | - | - |
|`model-name`| string |Name of the database collection.* The value is case sensitive |
> ***Tip**: Model name can also be passed through URL. In your app route configuration, define a variable `modelName` in the URL pattern.
##### example:
HTML:
```
<crud-create-model model-name="modelName"></crud-create-model>
```
JS:
```
app.controller('upsertCtrl', ['$scope', function ($scope) {
    $scope.modelName = 'User';
}]);
```
___
#####**crud-model-table**
Can be added either by element or attribute.
#####attributes: 
| Name | Type | Description |
| - | - | - |
|`model-name`| string |Name of the database collection.* The value is case sensitive |
|`handsontable-config`| object | Application specific configuration for handsontable|
> ***Tip**: Model name can also be passed through URL. In your app route configuration, define a variable `modelName` in the URL pattern.
##### example:
HTML:
```
<crud-model-table model-name="modelName" handsontable-config="tableConfig"></crud-model-table>
```
JS:
```
app.controller('listCtrl', ['$scope', function ($scope) {
    $scope.modelName = 'User';
    $scope.tableConfig = {
        columnSorting: true,
        sortIndicator: true
    };
}]);
```
___
#####**crud-property-table**
Can be added either by element or attribute. This directive is used for both to create and edit model. 
#####attributes: 
| Name | Type | Description |
| - | - | - |
|`form-options`| object | An object for form settings.<br><br> `id`(string): Value of the HTML `id` attribute of the form element. <br>`name`(string): Value of the HTML `name` attribute of the form element. <br>`isHorizontal`(boolean): `true` for horizontal alignment of the label and the input field of a property in the form. <br>`labelCol`(int): `col` count of the label of the property. This will be applied if the `isHorizontal` is set to `true`.
|`model`| object | The model to load in the table
|`config`| promise | The promise which results the configuration of the model to be loaded
|`actions`| object | An object having two functions: `save` and `cancel`. Extra steps on save and cancel action are passed through them.
> **Note**: To *create* a model don't specify the `model-id` attribute.
#####example:
HTML:
```
<crud-property-table form-options="formOptions" model="model" config="config" actions="actions"></crud-property-table>
```
JS:
```
app.controller('upsertCtrl', ['$scope', 'propertyHttpService', function ($scope, httpService) {
    $scope.formOptions = {
        id: 'form1',
        name: 'myForm',
        isHorizontal: true,
        labelCol: 4
    };
    
    var modelName = 'User';
    $scope.config = httpService.getConfig(modelName).then(function (response) {
        return response.data;
    });

    $scope.model = {
		"Id": "123...", // Id is the unique identifier of the data
		"FirstName": "Tajkia Rahman",
		"LastName": "Toma",
		"EmailAddress": "tajkiatoma@gmail.com",
		"RoleId": "56695504fa93d97877fa5c10",
		"IsRegular": true
	}

    $scope.actions = {
        save: function (data) {
            // do something
        },
        cancel: function () {
            // do something
        }
    };
}]);
```
___