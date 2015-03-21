# Important #
**This code is unmaintained for years and, while it still works, depends on a deprecated library (Interface).**

I recommend using the plugin from Manuele J Sarfatti http://mjsarfatti.com/sandbox/nestedSortable/, who spared me from all the work of having to port this to jQueryUI.


# Introduction #

The NestedSortable Widget (from now on just _widget_) is a component that allows you to create a multi-columned table of entries that can be sorted and nested very easily by your user, using drag-and-drop, with full support for pagination.

It is built on top of the plain [NestedSortable](NestedSortableDocumentation.md) plugin and will, basically, do almost all the work you would have to do if you were to use the regular [NestedSortable](NestedSortableDocumentation.md), as long as you provide it with a data source that will pass in your list of nested elements in JSON format. Given that list, it will build the HTML for the whole component, construct the [NestedSortable](NestedSortableDocumentation.md) and show visual clues as to when things are being loaded or saved.

The list items can have multiple columns, since the widget will draw your list in a way that looks like a table. The widget also has support for pagination of the list, both on the client (you don't have to show the whole list at once) and the server (you don't have to download the whole list at once). This can be a necessity when you are sorting a list with a large number of items. The widget code, perhaps surprisingly, is almost 3 times larger than the [NestedSortable plugin's](NestedSortableDocumentation.md) code. The good news is that all is already written for you and with it in your hands you will be able to create a list that can be sorted and nested by your user in no time.

You should [check the demo](http://www.bernardopadua.com/nestedSortables/test/widget/), to really get a grasp of its main functionalities. Note that the data source used in the demo is a little stupid: it won't save the order of the elements, it will just pretend it did.

This widget was designed as part of a Google Summer of Code project for WordPress, in 2007, in order to provide a easy way for users to sort their pages. You can see it in action for WordPress [in my blog](http://bitsinashortbit.wordpress.com/2007/09/16/the-final-bits-of-gsoc/).

# Download #

You can download the widget package (both the compressed and regular JS file, a CSS file, and 2 images) from the link bellow:

> [NestedSortable Widget 1.0](http://nestedsortables.googlecode.com/files/NestedSortableWidget-1.0.zip)

You can also get the uncompressed code, as well as the test code used in the demo:
> [NestedSortables Source 1.0](http://nestedsortables.googlecode.com/files/nestedSortables.1.0.complete.with.tests.zip)

You will also need the dependencies.


# Dependencies #

It was built on the latest versions of both jQuery and Interface.

  * [jQuery](http://code.google.com/p/jqueryjs/downloads/detail?name=jquery-1.2.pack.js) 1.1 or above (tested with 1.2)
  * [Interface 1.2](http://interface.eyecon.ro/interface/interface_1.2.zip) or above (only needs the components bellow, in that order)
    * iutil.js
    * idrag.js
    * idrop.js
    * isortables.js
  * [The plain NestedSortable plugin](http://nestedsortables.googlecode.com/files/inestedsortable-1.0.pack.js)
  * [toJSON plugin](http://jollytoad.googlepages.com/json.js) (only needed if you want to **send** the data back to the server in JSON format - just skip it if you are not sure)

Those dependencies have to be loaded in your HTML file in that order and before the widget itself.

# Usage #

## Basic Usage ##


First you need to set up your data source, which will provide the list of nested elements to be displayed by the widget, in JSON format. This data source should also be capable of receiving data from the widget, once the user saves the order of the elements, and update your database to reflect that. More details about this data source will be given bellow. For testing purposes, the easiest data source you could create is a plain text file with the contents of your list in JSON format. It obviously won't save your list order, but will allow you to view the list. So, create a file, named `my_list_json.js`, with the following contents:

```
{
  "requestFirstIndex" : 0,
  "firstIndex" : 0,
  "count": 4,
  "totalCount" : 4,
  "columns":["Title(ID)", "Owner", "Updated"],
  "items": 
  [
    {
      "id":1, 
      "info":["Page Title(1)", "Bernardo Pádua", "2007-06-09 2:44 pm"]
    },
    {
      "id":2, 
      "info":["Page Title(2)", "Bernardo Pádua", "2007-06-09 2:44 pm"],
      "children": 
        [
          {
            "id":3, 
            "info":["Page Title(3)", "Bernardo Pádua", "2007-06-09 2:44 pm"],
            "children": [
              {
                "id":4, 
                "info":["Page Title(4)", "Bernardo Pádua", "2007-06-09 2:44 pm"]
              }
            ]
          }
        ]
    }
  ]
}

```

Now include the dependencies and the plugin itself in your HTML file.

```
<script type="text/javascript" src="jquery.js"></script>
<script type="text/javascript" src="interface.js"></script>
<script type="text/javascript" src="inestedsortable.js"></script>
<script type="text/javascript" src="jquery.nestedsortablewidget.js"></script>
```

You will also need to include the CSS for the component, if you don't want it to look too ugly. Also be aware this CSS loads a couple of images (included in the download), so you need to make sure they are in your server as well.

```
<link rel="stylesheet" href="../../src/widget/style/nestedsortablewidget.css" type="text/css">
```

In you HTML file, create an empty HTML element (usually a `DIV`) where the component will be drawn. The contents of this element will be deleted once the widget is configured.

```
 <div id="my_widget"></div>
```

Add the JS code that will configure the widget. Once this code is executed, the widget will fetch the list contents from the loadUrl and display it for you.

```
jQuery( function($) {
  $('#my_widget').NestedSortableWidget({
	loadUrl: "my_list_json.js"
  });
});
```

## Configuration Parameters ##

The NestedSortable plugin will add three functions to the jQuery object: `NestedSortableWidget`, `NestedSortableWidgetDestroy` and `NestedSortableWidgetSave`. The first will configure the widget and takes a object of configuration parameters. The other two should be called on already built widgets and take no parameters.

`NestedSortableWidget` needs to be called on a jQuery object after selecting the element that will hold the component. You should probably use `$("#element_id").NestedSortableWidget({(...)})` to do it.

Like in the regular [NestedSortable](NestedSortableDocumentation.md), you configure the widget by passing a single object to the `NestedSortableWidget` method. Use the `{}` notation to create such object. Lots of things can be configured in the widget.

First, all the options of the underlying [NestedSortable](NestedSortableDocumentation.md) and Interface's Sortable can be altered, by passing an object to the `nestedSortCfg` property. You can also change the names of the classes used to draw the component, by passing an object to the `classes` property. You can also change the text used in the widget, by passing an object to the `text` property. Finally, other options change the way things are displayed, loaded and saved, and allow you to add callbacks that are executed at special moments of the widget's life.

The table bellow shows all the parameters that can be passed to the `NestedSortableWidget`.

| **Parameter Name**  | **Value Type** | **Type/Default** | **Description** |
|:--------------------|:---------------|:-----------------|:----------------|
| name | string | optional / `'nested-sortable-widget`' | The name of name of parameter that will be passed to the saveUrl when saving the page order. Also used internally for the element ids. If you want to have more than one widget in the same page,  you have to configure unique names for them. |
| loadUrl | string | mandatory | This is the URL that will return the JSON for the items list, by default via a GET request. If you want to pass query string parameters to it, use `loadUrlParams`.  |
| saveUrl | string | optional / same as loadUrl | This is the URL where the widget will save the altered items disposition, using 'POST' by default. This will, by default, be the same as loadUrl. If you want to pass query string parameters to it, use `saveUrlParams`. |
| loadUrlParams | Object | optional | A JS Object whose properties will be converted to a query string and passed to the loadUrl, along with the parameters the widget itself will add. |
| saveUrlParams | Object | optional | A JS Object whose properties will be converted to a query string and passed to the saveUrl, along with the parameters the widget itself will add. |
| loadRequestType| string | optional / `'GET'` | Can be set to 'GET' or 'POST' to set up what type of HTTP request you want to use when loading the items JSON. |
| saveRequestType| string | optional / `'POST'` | Can be set to 'GET' or 'POST' to set up what type of HTTP request you want to use when saving the items disposition. |
| serializeWithJSON | boolean | optional / `false` | By default, the items disposition will be sent to the server using the "query string parameters" standard, which most server side languages will interpret and generate the apropriate data structures (usually Arrays or Hashes) for you to access, automatically, in you server script. If you prefer, you can turn this on to send the items disposition in JSON format instead, which you will need to interpret on the server, probably using a library. This option requires the JSON jQuery plugin, listed in the dependencies. |
| onLoad| Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired whenever a  new page is fetched from the server, including the first one.  |
| onInitialLoad | Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired after the widget is loaded for the first time, but not after loading other pages. |
| onLoadError | Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired after there is an error loading data from the server. |
| onSave | Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired after the items disposition is successfully saved in the server.  |
| onSaveError | Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired after there is an error saving the items disposition in the server.   |
| onDestroy | Function | optional | This is a callback, to which you may pass in a variable that holds a reference to a JS function. All callbacks will receive a reference to the widget element as `this`. This callback will be fired after widget is destroyed using the `NestedSortableWidgetDestroy`. |
| nestedSortCfg | Object | optional | This object of properties will be passed to the underlying [NestedSortable](NestedSortableDocumentation.md) plugin, when they are constructed. Use it to set the any preferences you want for them. |
| colsWidth | float or Array of floats | optional / 150 | This will allow you to configure the size of the columns after the first one, which will auto-stretch. If you pass it a single float, all the columns will have the same width. Otherwise, pass in an array of floats (eg. [100, 150, 100] for pixels and [1.5, 2.5, 1.2] for ems - see `measureUnit`), with the column widths, from left to right, beginning with the second column. Note that the first column is set to auto-stretch, so you can't set a width for it. |
| whiteMargin| float | optional / 2 | The size of the white margin which divides the rows and columns in the table. |
| padding | float or Array of floats| optional / 4 | The padding that will be applied inside the cells of the table. If you pass it a single float, it will be the padding for all the directions. If you pass it an array of 4 floats, they will configure the padding in the following order: `[top, right, bottom, left]` |
| measureUnit | string | optional / 'px' | The measure unit used in the generated CSS for some of the dimensions specified above. |
| handle| boolean | optional / `false` | If you set this to true, instead of dragging the element by clicking in the entire row, the user will have to drag a handle that is placed in the first column of each row, whose text contents can be configured. |
| paginate | boolean | optional / `false` | If you set this to true, the widget will display the items across different pages, each with an approximate maximum number of items. All options bellow only work when `paginate` is `true`. |
| greedy | boolean | optional / `false` | When true, the widget will ask the server to send the whole list of items at once, and will cache those items, as the next pages are displayed. In effect, data will only be loaded from the server once, when the widget is built. When false, the default, the widget will only ask the server to send the items that are going to be displayed next, and will make new requests to the server whenever a page that wasn't loaded before is displayed.|
| incremental | boolean | optional / `false` | When true, the widget will display all the items together, in the same user view, and will augment the list incrementally. Eg.: when the user presses 'Next Entries', the items corresponding to the next page will be appended in the bottom of the list. This works but has some tiny bugs (if you are interested try it and see if they bother you). |
| itemsPerPage | integer | optional / `10` | How many items, at least, will be shown on each page. The widget will always show complete hierarchies in a page, and sometimes more elements will have to be shown. Eg.: You are paging at 10 items per page, your 9th item is a root element with 5 children, in this case all its childrens will be displayed and you will have 14 items in one page. |
| startPage | integer | optional / `1` | Sometimes you may want to begin displaying the list from somewhere in the middle of it. If you have, say, 10 items per page and set this as 5, the first page the widget will display will have (approximately) items from indexes 40 to 50.  |
| pageChangeTimer | integer | optional / `"500"` | When you hover an item on the the page change box, it will wait a while until it changes the page, to prevent unwanted page changes. You can set this amount of time here, in miliseconds. |
| fadeOutHover | boolean | optional / `true` | When set to true, the page change box will gradually fade as it gets closer to the time the next page will be loaded. This is currently disabled when using jQuery 1.2, as an Interface 1.2 incompatibility will prevent it from working.  |
| transitionAnim | string | optional / `'slide-parallel'` | When changing from one page to the next, the transition is so quick it might not be noticed by the user. So, by default, we apply an animation to make it obvious to the user something was loaded. The "new page in" and "old page out" animations may be applied in parallel or series. The predefined options are `"slide-parallel"`, `"slide-series"`, `"fade-series"`, `"normal-parallel"`, `"normal-series"` and `"none"` (no animation at all). You may also define custom transitions, by setting this to `"custom-series"` or `"custom-parallel"` and defining the animation functions using the callbacks bellow. |
| transitionOut | Function | optional | Pass in a function that should make the old page disappear, probably using an animation. `this` inside your callback will point to the  jQuery object with the element in question selected. Also, a single parameter will be passed in to your callback, with a function. You must execute this function right after your animation ends (probably by passing it as a callback to you animation function).  |
| transitionIn | Function | optional | Pass in a function that should make the new page appear, probably using an animation. `this` inside your callback will point to the  jQuery object with the element in question selected. Also, a single parameter will be passed in to your callback, with a function. You must execute this function right after your animation ends (probably by passing it as a callback to you animation function). |

## More Details about Pagination ##

In the [demo](http://www.bernardopadua.com/nestedSortables/test/widget/) I am showing the widget being used with pagination on a dataset of about 30 items. I put up a simple PHP page as a demo data source that will spit clusters of a bigger JSON with the whole data, according to the page requested. Optionally, you may simplify the data source and always reply with the whole dataset, pagination will still happen on the client side. Or your data source may be “greedy” and reply with a bigger chunk than the one requested. The component will be smart enough not to request any data it already has. Using Firebug in Firefox allows you to see very easily when and what data is being requested/returned.

You will notice that in my demo a page won’t always have 5 items, sometimes it will have more: this is by design, as if I was to enforce only 5 items per page we would have to break hierarchies in half, what would be weird for the user and terrible to program as well.

Initially, when the widget is loaded, it requests the first item of the page being loaded. If loading the third page with 5 elements per page, that would be the element with the zero-based index order of 15. But it will actually only display the first element after the one with index 15 that doesn’t have any parents - we will call it a ‘root’ element from now on. And it will display at least 5 items, since it will always show complete hierarchies.

When the component needs some data to display a page, it will make an HTTP request with 2 parameters:

  * `firstIndex` (what is the zero-based ordered position of the next item I want to fetch and display. Note the component actually wants to display, as the first item in the page being shown to the user, the first root element after it, but it doesn’t know in advance what it is )
  * `count` (how many items, at minimum, should be returned)

If those parameters are not supplied, your server side script should understand the widget is requesting the whole list (when there is no pagination or the `greedy` option is on).

The server should reply with the data, in JSON format, that, besides the data itself, has the following parameters:

  * `requestFirstIndex` (what firstIndex was requested)
  * `count` (how many items were actually returned, which should be greater or equal to what was requested)
  * `firstIndex` (what was the index of the first element actually returned; ideally, it should be the first root item after requestFirstIndex , but it could be also be any root element before it, it could even be ‘0′, if the server decided to return the whole dataset )
  * `totalCount` (how many items does the dataset has in total)

Note that the server should always return the whole data that the component wanted to display. Eg: if we request firstIndex = 10, count=5, the server could return firstIndex = 5, count=20, but not firstIndex = 5, count=8.

## Data Source ##

There are two basic things you will need to implement on the server to be able to use the widget: Loading and Saving. This is what we are calling the "data source".

### Loading ###

Loading is basically fetching your list of items and sending it to the widget in JSON format. This is fairly easy, as there are lots of JSON libraries out there, all you will have to do is generate an array or equivalent in your server side language. If you want pagination on the server side, in order to only send the user the data he is going to display in the next page, you need to read the `firstIndex` and `count` request parameters that will be passed in and only return the data in that range, following the rules given in the **More Details about Pagination** section, of not breaking hierarchies. If you read **Basic Usage** and **More Details about Pagination** you should have a good idea of the format of the JSON data you need to return. You could look at the `tests/widget` folder at the [widget's source archive](http://nestedsortables.googlecode.com/files/nestedSortables.1.0.complete.with.tests.zip) for a simple implementation of the loading operation in PHP.

### Saving ###

Saving should be pretty easy as well. By default, your script will be passed in an array/hash in the form of query string parameters, so you should be able to access it instantaneously in your server side script. I will give a PHP representation of an example which should make the format pretty obvious. This is what I call a "cluster" of data sent by the widget:

```
/*
Represents the following disposition of 7 items
  1
  3
  2
  5
  |- 4
    |- 6
    |- 7
*/
$REQUEST['nested-sortable-widget']['firstIndex'] = 0 
$REQUEST['nested-sortable-widget']['count'] = 7
$REQUEST['nested-sortable-widget']['items'] = 
  array(
    array(
      "id" => 1
    ),
    array(
      "id" => 3
    ),
    array(
      "id" => 2
    ),
    array(
      "id" => 5, 
      "children" =>
        array(
          "id" => 4, 
          "children"=>
            array(
              array(
                "id" => 6
              ),
              array(
                "id" => 7
              )
            )
          }
        )
    )
  );
```

`nested-sortable-widget` is the default name for the parameter, but the parameter name will be the name of your widget (see `name` under configuration options).

When pagination is enabled, the widget will only send to the server items in the pages that were loaded AND whose order was altered. There are two details your save script will need to be aware of then:

  * Your script can receive more than one cluster at once. In that case, `$REQUEST['nested-sortable-widget']` will be a numbered array, whose elements will be clusters like the one given above. That will happen when the user alters, for instance, only page 1 and page 3, but leaves page 2 untouched. In this example we would have two clusters `$REQUEST['nested-sortable-widget'][0]` and `$REQUEST['nested-sortable-widget'][1]`. If only one cluster is sent it will be accessed directly from `$REQUEST['nested-sortable-widget']`, so you need to check if the parameter passed in is a numeric array.
  * Your script needs to understand the `firstIndex` parameter: it is the "ordered" index of the first item whose order is being saved.

There is one trap at which you may fall when using pagination and when you save the order of your pages using numeric indexes: You will need to increment the order of all the elements following the items of the page being saved. This can be done by running a single SQL UPDATE.

To make this section more colorful, I will provide a real life save method, from my WordPress implementation, that saves one of those clusters of items (the items in WordPress are "pages"). This is a recursive function, and $pages\_array will initially receive the contents of `$REQUEST['nested-sortable-widget']['items']`.

```
function save_page_order($pages_array, $parent_id = 0) {
  global $wpdb;
  $first_page = $wpdb->escape($pages_array[0]['id']);
  $current_menu_order = get_post_field('menu_order', $first_page, 'db');
  if(is_wp_error($current_menu_order)) return false;
  if($parent_id === 0) {
    //shifts the menu order for all the root pages after the ones we
    //will alter
    $num_root_pages = count($pages_array);
    $query_ret = $wpdb->query("UPDATE $wpdb->posts SET menu_order = menu_order + $num_root_pages WHERE  post_type = 'page' AND post_parent = 0 AND menu_order > $current_menu_order");
    if($query_ret === false) return false;
    }
    foreach ($pages_array as $index => $page) {
    $page_id = $wpdb->escape($page['id']);

    $query_ret = $wpdb->query("UPDATE $wpdb->posts SET post_parent = '$parent_id', menu_order = '$current_menu_order' WHERE id ='$page_id'");

    if($query_ret === false) return false;

    $current_menu_order++;
    if (is_array($page['children'])) {
      //does it for the children as well
      if (!$this->save_page_order($page['children'], $page_id)) return false;
    }
  }
  return true;
}
```

# Known Bugs #

As of version 1.0, there are 2 minor cosmetic bugs, one for IE6 and the other in Safari. They are not show stoppers. There is another bug in Incremental mode, which is only a little annoying, so I don't recommend using incremental mode.

Refer to [the issues list for more details](http://code.google.com/p/nestedsortables/issues/list?q=label:NestedSortableWidget).

Interface 1.2 was built for jQuery 1.1 and does have a few issues, specially with animations, when you use it with jQuery 1.2. So, with jQuery 1.2, you might encounter issues with the animation options in the NestedSortables, which are inherited from Interface. jQuery 1.2 was just released, lets hope Interface is updated to support it. The combination used in the demo is using jQuery 1.2 and Interface 1.2, and at least in those examples, doesn't generate any errors. The `fadeOutHover` option will be deactivated when using jQuery 1.2, due to one of those incompatibilities.

# TODO for next versions #

  * More configuration options (as needed by the users)


