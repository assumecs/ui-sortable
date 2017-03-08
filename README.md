# UI.Sortable directive
[![npm](https://img.shields.io/npm/v/angular-ui-sortable.svg)](https://www.npmjs.com/package/angular-ui-sortable)
[![npm](https://img.shields.io/npm/dm/angular-ui-sortable.svg)](https://www.npmjs.com/package/angular-ui-sortable)
[![Build Status](https://travis-ci.org/angular-ui/ui-sortable.svg?branch=master)](https://travis-ci.org/angular-ui/ui-sortable)
[![Coverage Status](https://coveralls.io/repos/angular-ui/ui-sortable/badge.svg?branch=master)](https://coveralls.io/r/angular-ui/ui-sortable?branch=master)
[![debugInfoEnabled(false) Ready Badge](https://rawgit.com/thgreasi/ng-debugInfoDisabled-badges/master/badge1.svg)](https://docs.angularjs.org/guide/production#disabling-debug-data)
[![Join the chat at https://gitter.im/angular-ui/ui-sortable](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/angular-ui/ui-sortable?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This directive allows you to sort an array with drag & drop.

## Requirements

- JQuery v3.1+ (for jQuery v1.x & v2.x use [v0.14.x versions](https://github.com/angular-ui/ui-sortable/tree/v0.14.x-stable))
- JQueryUI v1.12+
- AngularJS v1.2+

[Single minified cdn link](http://cdn.jsdelivr.net/g/jquery@1,jquery.ui@1.10%28jquery.ui.core.min.js+jquery.ui.widget.min.js+jquery.ui.mouse.min.js+jquery.ui.sortable.min.js%29,angularjs@1.2,angular.ui-sortable) ~245kB and [example](http://codepen.io/thgreasi/pen/olDJi) with JQuery v1.x, required parts of JQueryUI v1.10, AngularJS v1.2 & latest angular-ui-sortable.

**Notes:**
> * JQuery must be included before AngularJS.  
> * JQueryUI dependecies include [core](http://api.jqueryui.com/category/ui-core/), [widget](http://api.jqueryui.com/jQuery.widget/), [mouse](http://api.jqueryui.com/mouse/) & [sortable](http://api.jqueryui.com/sortable/). Creating a [custom build](http://jqueryui.com/download/#!version=1.10&components=1110000010000000000000000000000000) will [greatly reduce](https://github.com/angular-ui/ui-sortable/issues/154#issuecomment-40279430) the required file size. ([CDN](http://www.jsdelivr.com/) links for comparison: [full](http://cdn.jsdelivr.net/g/jquery.ui@1.10) vs  [minimal](http://cdn.jsdelivr.net/g/jquery.ui@1.10%28jquery.ui.core.min.js+jquery.ui.widget.min.js+jquery.ui.mouse.min.js+jquery.ui.sortable.min.js%29))
> * Users of AngularJS pre v1.2 can use [v0.10.x](https://github.com/angular-ui/ui-sortable/tree/v0.10.x-stable) or [v0.12.x](https://github.com/angular-ui/ui-sortable/tree/v0.12.x-stable) branches.
> * Early adopters of [Angular2](https://angular.io/) can use the  [ng2 branch](https://github.com/angular-ui/ui-sortable/tree/ng2).

## Installation

* Install with Bower `bower install -S angular-ui-sortable`
* Install with npm `npm install -S angular-ui-sortable`
* Download one of the [Releases](https://github.com/angular-ui/ui-sortable/releases) or the [latest Master branch](https://github.com/angular-ui/ui-sortable/archive/master.zip)

## Usage

Load the script file: sortable.js in your application:

```html
<script type="text/javascript" src="modules/directives/sortable/src/sortable.js"></script>
```

Add the sortable module as a dependency to your application module:

```js
var myAppModule = angular.module('MyApp', ['ui.sortable'])
```

Apply the directive to your form elements:

```html
<ul ui-sortable ng-model="items">
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```

**Developing Notes:**

* `ng-model` is required, so that the directive knows which model to update.
* `ui-sortable` element should contain only one `ng-repeat`, but other non-repeater elements above or below may still exist.
  Otherwise the index matching of the `ng-model`'s items and the DOM elements generated by the `ng-repeat` will break.  
  **In other words: The items of `ng-model` must match the indexes of the DOM elements generated by the `ng-repeat`.**
* [`Filters`](https://docs.angularjs.org/guide/filter) that manipulate the model (like [filter](https://docs.angularjs.org/api/ng/filter/filter), [orderBy](https://docs.angularjs.org/api/ng/filter/orderBy), [limitTo](https://docs.angularjs.org/api/ng/filter/limitTo),...) should be applied in the `controller` instead of the `ng-repeat` (refer to [the provided examples](#examples)).  
This is the preferred way since it:
  - is performance wise better
  - reduces the chance of code duplication
  - [is suggested by the angularJS team](https://www.youtube.com/watch?feature=player_detailpage&v=ZhfUv0spHCY#t=3048)
  - it does not break the matching of the generated DOM elements and the `ng-model`'s items
* `ui-sortable` lists containing many 'types' of items can be implemented by using dynamic template loading [with ng-include](http://stackoverflow.com/questions/14607879/angularjs-load-dynamic-template-html-within-directive/14621927#14621927) or a [loader directive](https://github.com/thgreasi/tg-dynamic-directive), to determine how each model item should be rendered. Also take a look at the [Tree with dynamic template](http://codepen.io/thgreasi/pen/QKmWKj) example.

### Options

All the [jQueryUI Sortable options](http://api.jqueryui.com/sortable/) can be passed through the directive.  
Additionally, the `ui` argument of the available callbacks gets enriched with some extra properties as specified to the [API.md file](API.md#uiitemsortable-api-documentation).
Any model changes that happen inside the available callbacks, are applied right after the stop event. We are not wrapping callbacks like `start`/`change`/... with `$apply`, in order to minimize the number of digest loops and avoid possible modifications of the model (eg: by watchers) before the drop takes place.

```js
myAppModule.controller('MyController', function($scope) {
  $scope.items = ["One", "Two", "Three"];

  $scope.sortableOptions = {
    update: function(e, ui) { ... },
    axis: 'x'
  };
});
```

```html
<ul ui-sortable="sortableOptions" ng-model="items">
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```

When using event callbacks ([start](http://api.jqueryui.com/sortable/#event-start)/[update](http://api.jqueryui.com/sortable/#event-update)/[stop](http://api.jqueryui.com/sortable/#event-stop)...), avoid manipulating DOM elements (especially the one with the ng-repeat attached).
The suggested pattern is to use callbacks for emmiting events and altering the scope (inside the 'Angular world').

#### Floating

To have a smooth horizontal-list reordering, jquery.ui.sortable needs to detect the orientation of the list.
This detection takes place during the initialization of the plugin (and some of the checks include: whether the first item is floating left/right or if 'axis' parameter is 'x', etc).
There is also a [known issue](https://bugs.jqueryui.com/ticket/7498) about initially empty horizontal lists.

To provide a solution/workaround (till jquery.ui.sortable.refresh() also tests the orientation or a more appropriate method is provided), ui-sortable directive provides a `ui-floating` option as an extra to the [jquery.ui.sortable options](http://api.jqueryui.com/sortable/).

```html
<ul ui-sortable="{ 'ui-floating': true }" ng-model="items">
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```

**OR**

```js
$scope.sortableOptions = {
  'ui-floating': true
};
```
```html
<ul ui-sortable="sortableOptions" ng-model="items">
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```


**ui-floating** (default: undefined)  
Type: [Boolean](http://api.jquery.com/Types/#Boolean)/[String](http://api.jquery.com/Types/#String)/`undefined`
*   **undefined**: Relies on jquery.ui to detect the list's orientation.
*   **false**:     Forces jquery.ui.sortable to detect the list as vertical.
*   **true**:      Forces jquery.ui.sortable to detect the list as horizontal.
*   **"auto"**:    Detects on each drag `start` if the element is floating or not.

#### Attributes For Event Handling

To handle events with html bindings just define any expression to listed event attributes. 
If you defined an attribute for this events and defined callback function in sortableOptions at the same time, the attribute based callback will be called first.

*   **ui-sortable-activate**
*   **ui-sortable-start**
*   **ui-sortable-activate**
*   **ui-sortable-before-stop**
*   **ui-sortable-update**
*   **ui-sortable-remove**
*   **ui-sortable-receive**
*   **ui-sortable-deactivate**
*   **ui-sortable-stop**



Expression works on update event.
```html
<ul ui-sortable ng-model="items" ui-sortable-update="expression" >
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```


On update event callBackFunction1 if called before callBackFunction2.
```js
$scope.sortableOptions = {
  'update': callBackFunction2
};
```
```html
<ul ui-sortable="sortableOptions" ng-model="items" ui-sortable-update="callBackFunction1" >
  <li ng-repeat="item in items">{{ item }}</li>
</ul>
```

#### Canceling

Inside the `update` callback, you can check the item that is dragged and cancel the sorting.

```js
$scope.sortableOptions = {
  update: function(e, ui) {
    if (ui.item.sortable.model == "can't be moved") {
      ui.item.sortable.cancel();
    }
  }
};
```

**Notes:**
* `update` is the appropriate place to cancel a sorting, since it occurs before any model/scope changes but after the DOM position has been updated.
So `ui.item.scope` and the directive's `ng-model`, are equal to the scope before the drag start.
* To [cancel a sorting between connected lists](https://github.com/angular-ui/ui-sortable/issues/107#issuecomment-33633638), `cancel` should be called inside the `update` callback of the originating list. A simple way to is to use the `ui.item.sortable.received` property:
```js
update: function(event, ui) {
  if (// ensure we are in the first update() callback
      !ui.item.sortable.received &&
      // check that its an actual moving between the two lists
      ui.item.sortable.source[0] !== ui.item.sortable.droptarget[0] &&
      // check the size limitation
      ui.item.sortable.model == "can't be moved between lists") {
    ui.item.sortable.cancel();
  }
}
```

### jQueryUI Sortable Event order

**Single sortable** [demo](http://codepen.io/thgreasi/pen/QKmWGj)
```
create

/* dragging starts */
helper
start
activate

/* multiple: sort/change/over/out */

beforeStop
update    <= call cancel() here if needed
deactivate
stop
```

**Connected sortables** [demo](http://codepen.io/thgreasi/pen/YGazpJ)

```
list A: create
list B: create

/* dragging starts from sortable A to B */
list A: helper
list A: start
list B: activate
list A: activate

/* both lists multiple: sort/change/over/out */
list A: sort
list A: change
list B: change
list B: over
list A: sort
list B: out
list A: sort

list A: beforeStop
list A: update    <= call cancel() here if needed
list A: remove
list B: receive
list B: update
list B: deactivate
list A: deactivate
list A: stop
```

For more details about the events check the [jQueryUI API documentation](http://api.jqueryui.com/sortable/).

## Integrating with directives doing transclusion
Wrap the transclusion directive element with the ui-sortable directive and set the `items` to target your `ng-repeat`ed elements. Following best practices, it is also highly recommended that you add a `track by` expression to your `ng-repeat`. [Angular Meterial example](http://codepen.io/thgreasi/pen/NbyLVK).

```js
myAppModule.controller('MyController', function($scope) {
  $scope.items = ["One", "Two", "Three"];

  $scope.sortableOptions = {
    items: '.sortable-item'
    // It is suggested to use the most specific cssselector you can,
    // after analyzing the DOM elements generated by the transclusion directive
    // eg: items: '> .transclusionLvl1 > .transclusionLvl2 > .sortable-item'
  };
});
```

```html
<div ui-sortable="sortableOptions" ng-model="items">
  <a-transclusion-directive>
    <div ng-repeat="item in items" class="sortable-item">{{ item }}</div>
  </a-transclusion-directive>
</div>
```

## Examples

- [Simple Demo](http://codepen.io/thgreasi/pen/wzmvgw)
  - [Simple Attribute Handlers Demo](http://codepen.io/thgreasi/pen/JWbmjb)
  - [Simple RequireJS Demo](http://codepen.io/thgreasi/pen/rrdNjj)
  - [Simple Touch-Enabled Demo](http://codepen.io/thgreasi/pen/wzmvJv) using [jQuery UI Touch Punch](https://github.com/furf/jquery-ui-touch-punch/)
  - [Multiple items sorting (ctrl+click)](http://codepen.io/thgreasi/pen/mJAcL) using [ui-sortable-multiselection](https://github.com/thgreasi/ui-sortable-multiselection)
- [Connected Lists](http://codepen.io/thgreasi/pen/ozqNkr)
- [Filtering](http://codepen.io/thgreasi/pen/XjEWRa) ([details](https://github.com/angular-ui/ui-sortable/issues/113))
- [Ordering 1](http://codepen.io/thgreasi/pen/PGRomA) & [Ordering 2](http://plnkr.co/edit/gYsZZdFLeoeb5N9iemAO?p=preview) ([details](https://github.com/angular-ui/ui-sortable/issues/70))
- [Cloning](http://codepen.io/thgreasi/pen/jrzOLA) ([details](https://github.com/angular-ui/ui-sortable/issues/139))
- [Horizontal List](http://codepen.io/thgreasi/pen/zKWYdP)
- [Tree with dynamic template](http://codepen.io/thgreasi/pen/VKXwzV)
- Canceling
  - [Connected Lists With Max Size](http://codepen.io/thgreasi/pen/zKWYEP)
  - [Connected Lists Without Duplicates](http://codepen.io/thgreasi/pen/PGRoJr)
  - [Promised Reverting](http://codepen.io/thgreasi/pen/ORvJzJ)
- [Locked Items](http://codepen.io/thgreasi/pen/ALyBVQ)
- [Draggable Handle](http://codepen.io/thgreasi/pen/PGRoRY)
- [Drop Zone](http://codepen.io/thgreasi/pen/zKWYWR)
- [Draggable-Sortable like interaction](http://codepen.io/thgreasi/pen/mAxdxk)
- [Static HTML Sorting](http://codepen.io/thgreasi/pen/XjEWqV)

## Integrations
- [firebase](http://codepen.io/thgreasi/pen/repEZg?editors=0010)
- [ui.bootstrap.accordion](http://plnkr.co/edit/TGIeeEbbvJwpJ3WRqo2z?p=preview)
- [Angular Meterial](http://codepen.io/thgreasi/pen/NbyLVK) (thanks yenoh2)
- [Asynchronous loading jQuery+jQueryUI with crisbeto/angular-ui-sortable-loader](https://github.com/crisbeto/angular-ui-sortable-loader)

## Reporting Issues

The [above](#examples) pen's are provided as a good starting point to demonstrate issues, proposals and use cases.
Feel free to edit any of them for your needs (don't forget to also update the libraries used to your version).

## Testing

We use Karma and jshint to ensure the quality of the code.  The easiest way to run these checks is to use grunt:

```sh
npm install -g grunt-cli
npm install && bower install
grunt
```

The karma task will try to open Firefox and Chrome as browser in which to run the tests.  Make sure this is available or change the configuration in `test\karma.conf.js`.


### Grunt Serve

We have one task to serve them all!

```sh
grunt serve
```

It's equal to run separately:

* `grunt connect:server` : giving you a development server at [http://127.0.0.1:8000/](http://127.0.0.1:8000/).

* `grunt karma:server` : giving you a Karma server to run tests (at [http://localhost:9876/](http://localhost:9876/) by default). You can force a test on this server with `grunt karma:unit:run`.

* `grunt watch` : will automatically test your code and build your demo.  You can demo generation with `grunt build:gh-pages`.
