Source: https://wealthsimple.quip.com/VCBBAVBGzrSz

Front End Guidelines
====================

We have standardized our javascript code to use Angular as a web framework. Here are some helpful guidelines for organizing and structuring angular code:

Inpiration
----------

* https://github.com/johnpapa/angular-styleguide
* https://www.youtube.com/watch?v=MpI3HKDVZm0
    * Slides: https://docs.google.com/presentation/d/1CITTvkEaR7gZTtrL8202_iP8jWVzn7--Er615lnOJic/edit#slide=id.g183c4fdab_0308

Guide
=====

0.0 Unit testing
----------------

* Each component needs an appropriate unit test

1.0 Naming
----------

**1.1 Filesystem**

* use lowercase for files and folders
* Dashes-between-words

my-folder/my-file.css

**1.2 Suffix the files**

* Required for compilation pipeline
* Helps distinguish components in a module directory
* Module: app.module.js
* Controller: product-list.controller.js
* Directive: product-list.directive.js

* Tests: product-list.test.js
* Template: product-list.html
* CSS: product-list.css

**1.3 Namespace the components**

**FileSystem:**
src/js/core/main.controller.js
**
Angular: **
angular.module(“terminal”)
 .controller
  (“terminal.core.mainCtrl”,
   [“$scope”, function($scope){

}]);

2.0 Organization
----------------

**2.1 One component per file**

* Declare only one component per file.
* Easier to locate components
* Shorter files

**2.2 Folder structure**

* Core → App sections
* Common → Repeated through core
* Nested States = Nested Folders

scripts/ 
    common/ 
        services/ 
        directives/
    core/
        app.mdl.js
        products/
            products.contoller.js
            product-details/

**2.3 Keep everything together**

* Easy to find related files
* Prevents folder structure duplication

users-list/
    users-list.controller.js
    users-list.controller.test.js
    users-list.html
    users-list.css
smart-tree/
    smart-tree.directive.js
    smart-tree.directive.test.js
    smart-tree.html
    smart-tree.css

**2.4 Separate module for each component**

**src/js/terminal/user/user.module.js**
.module(“terminal.user”, [])

**src/js/terminal/user/user.controller.js**
.controller(
 “myApp.core.user.userCtrl”, ...)


**src/js/common/services/product.service.js**
angular
.module(“ws.common.services”)
 .service(
  “ws.common.services.product”


**2.5 Wrap angular components in IIFE**

/**
* recommended
*
* no globals are left behind
*/

// logger.js
(function() {
  'use strict';

  angular
    .module('app')
    .factory('logger', logger);

  function logger() { }
})();

**2.6 Avoid declaring modules with variable names**

/* avoid */
var app = angular.module('app', [
  'ngAnimate',
  'ngRoute',
  'app.shared',
  'app.dashboard'
]);

/* recommended */
angular
  .module('app', [
    'ngAnimate',
    'ngRoute',
    'app.shared',
    'app.dashboard'
  ]);

**2.7 Use Named functions**

/* recommended */

// dashboard.js
angular
  .module('app')
  .controller('DashboardController', DashboardController);

function DashboardController() { }

**2.8 Bindable methods up top**

/* recommended */
function SessionsController($scope) {

  $scope.gotoSession = gotoSession;
  $scope.refresh = refresh;
  $scope.search = search;
  $scope.sessions = [];
  $scope.title = 'Sessions';

  ////////////

  function gotoSession() {
    /* */
  }

  function refresh() {
    /* */
  }

  function search() {
    /* */
  }
}

**Use function declaration instead of variable assignment**

var activate = function() {
  return getAvengers().then(function() {
    logger.info('Activated Avengers View');
  });
}

/* Prefer */
function activate() {
  return getAvengers().then(function() {
    logger.info('Activated Avengers View');
  });
}


**Defer Controller Logic to Services**

* Services are more testable
* Keep controllers for view updates only

**Services**

* Are instantiated. Use this for public methods and variables

**Factories**

* Single responsibility
* Singletons: return an object that contains members of its service.
* Keep exposed members at the top:

/* recommended */
function dataServiceFactory() {
  var someValue = '';
  var service = {
    save: save,
    someValue: someValue,
    validate: validate
  };
  return service;

  ////////////

  function save() {
    /* */
  }

  function validate() {
    /* */
  }
}

**Data Services**

* Make data operations (Ajax calls, local storage, stashing in memory) into a factory.
* Return promises from data calls
    * Allows chaining

**Directives**

* Limit 1 per file
* Provide unique directive prefix since they are part of global namespace.

**Dependencies**

* Use $inject to manually identify your dependencies for Angular components.

/* recommended */
angular
  .module('app')
  .controller('DashboardController', DashboardController);

DashboardController.$inject = ['$location', '$routeParams', 'common', 'dataservice'];

function DashboardController($location, $routeParams, common, dataservice) {
}

**Exception Handling**

* Use decorators

/* recommended */
angular
  .module('blocks.exception')
  .config(exceptionConfig);

exceptionConfig.$inject = ['$provide'];

function exceptionConfig($provide) {
  $provide.decorator('$exceptionHandler', extendExceptionHandler);
}

extendExceptionHandler.$inject = ['$delegate', 'toastr'];

function extendExceptionHandler($delegate, toastr) {
  return function(exception, cause) {
    $delegate(exception, cause);
    var errorData = {
      exception: exception,
      cause: cause
    };
    /**
     * Could add the error to a service's collection,
     * add errors to $rootScope, log errors to remote web server,
     * or log locally. Or throw hard. It is entirely up to you.
     * throw exception;
     */
    toastr.error(exception.msg, errorData);
  };
}

**Route Errors**

* Handle and log all routing errors using $routeChangeError (https://docs.angularjs.org/api/ngRoute/service/$route#$routeChangeError).

/* recommended */
var handlingRouteChangeError = false;

function handleRoutingErrors() {
  /**
   * Route cancellation:
   * On routing error, go to the dashboard.
   * Provide an exit clause if it tries to do it twice.
   */
  $rootScope.$on('$routeChangeError',
    function(event, current, previous, rejection) {
      if (handlingRouteChangeError) { return; }
      handlingRouteChangeError = true;
      var destination = (current && (current.title ||
        current.name || current.loadedTemplateUrl)) ||
        'unknown target';
      var msg = 'Error routing to ' + destination + '. ' +
        (rejection.msg || '');
      /**
       * Optionally log using a custom service or $log.
       * (Don't forget to inject custom service)
       */
      logger.warning(msg, [current]);

      /**
       * On routing error, go to another route/state.
       */
      $location.path('/');

    }
  );
}

4.0 Misc
--------

**4.1 Use 2 space indent**

**4.2 Prefer promises over callbacks**

* Follow “ing” convention
    * function that have an “ing” verb return promises

function reading(){
  var deferred = $q.defer();
  oldSchool.read(function(a){
    deferred.resolve(a);
  });
  return deferred.promise
}

**4.3 Single Responsibility Principle**

* Each service has only one responsibility
* Makes for testable code and better modularization

**4.4 Name modules and components with namespaces to avoid colision**

(function() {
  'use strict';

  angular.module('ws.hercules.common.constants', []);

  angular
    .module('ws.hercules.common.constants')
    .factory('ws.hercules.common.constants.service', ConstantsService);
  ConstantsService.$inject = ['$resource'];
  function ConstantsService($resource) { }
})();

[See here for a potential starting point](https://github.com/airbnb/javascript)
