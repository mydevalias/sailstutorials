# CRUD example using sails js and angular only

This is basic app and there are many way this can be improved.

1) Install sails js: a [Sails Installation](http://sailsjs.org/get-started)
I assume you already have node.js

2) Create a new app:

`sails new crud`

and lift the server:

`cd crud
sails lift`

Test that everything works: [http://localhost:1337](http://localhost:1337) You should see the default home page
1337 is the default port. It should also be displayed when you lift the server.

3) create: api/models/Crud.js and add following code:
sails generate api Crud

```
module.exports = {

  attributes: {
    name: {
      type: 'string'
    }
  }
};
```

The model class (called Crud) will have only 1 field: name

4) Disable layout: config/views.js

`layout: false`

5) Add angular to project: assets/js/dependencies/external/angular.js
Grunt will handle the import in document.

6) Create route to new homepage: config/routes.js

```
 '/': {
    view: 'index'
  }
```

7) create views/index.ejs


```
<!DOCTYPE html>
<html ng-app="crudApp">
```
reference to ng-app that is created in js
```
<head>
  <title>sail.js and angular.js app</title>
  <!--STYLES-->
  <!--STYLES END-->
```
This will be used by Grunt to import styles
```
</head>

<body>
<div id="cruds" ng-controller="CrudCtrl">
  <div ng-repeat="crud in cruds">
    {{crud.name}} #{{crud.id}} <a ng-click="removeCrud($index)">delete</a>
    <span ng-hide="editing" ng-click="editing = true">edit</span>

    <div ng-show="editing">
      <input type="text" ng-model="crud.name" placeholder="Name" ng-required/>
      <button ng-click="editCrud($index); editing = false" ng-required>Save</button>
    </div>

  </div>
  <div>
    <input type="name" ng-model="newName"/>
    <button ng-click="addCrud()" ng-required>Add</button>
  </div>

</div>

<!--SCRIPTS-->
<!--SCRIPTS END-->
```
This will be used by Grunt to import scripts
```
<script>
```
```javascript
  var crudApp = angular.module('crudApp', []);
  crudApp.controller('CrudCtrl', function ($scope, $http) {
    $scope.cruds = [];

    $http.get("crud").success(function (data) {
      $scope.cruds = data;
    }).error(function () {
      alert("not working");
    })

    $scope.addCrud = function () {
      var data = {name: $scope.newName}
      $http.post("crud/create", data).success(function (data, status, header) {
        $scope.cruds.push(data);
        $scope.newName = "";
      })
    };

    $scope.removeCrud = function (index) {
      $http.post("crud/destroy", $scope.cruds[index]).success(function (resp) {
        $scope.cruds.splice(index, 1);
      })
    };

    $scope.editCrud = function (index) {
      $http.post("crud/update/"+$scope.cruds[index].id, $scope.cruds[index]).success(function (resp) {
        //$scope.cruds.splice(index, 1);
      })
    }
  });

  function removeObjFromArray(array, obj) {
    return array.filter(function (val) {
      return val !== obj;
    });

  }
  ;
</script>
</body>
</html>
```

Links:
a [Sails](http://sailsjs.org) application
