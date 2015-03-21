# Important #
**This code is unmaintained for years and, while it still works, depends on a deprecated library (Interface).**

I recommend using the plugin from Manuele J Sarfatti http://mjsarfatti.com/sandbox/nestedSortable/, who spared me from all the work of having to port this to jQueryUI.


# Introduction #

The NestedSortable [jQuery](http://jquery.com/) Plugin is built on top of the popular [Interface plugin](http://interface.eyecon.ro). jQuery is a "no-nonsense", fast, small, and extremelly easy to use JavaScript framework that is taking the world by storm. NestedSortable provides you with a way to easily build lists of elements that can be vertically sorted and nested (eg. one element can be made son of another) at the same time, using "Drag and Drop". It is based on [Interface's Sortables](http://interface.eyecon.ro/docs/sort) and works on top of it. So, a NestedSortable will also have all the configuration options which are present in a regular Sortable in Interface. Some examples of possible usages:

  * Organizing a table of contents
  * Organizing a list of hierarchical pages (it was originally created with this goal, for WordPress)
  * Organizing categories
  * Organizing files and folders

Perhaps the easier way to get what it does it by [looking at the demo](http://www.bernardopadua.com/nestedSortables/test/nested_sortable/). This plugin was designed as part of a Google Summer of Code project for WordPress, in 2007.

# Change Log #

## 1.0 ##
  * Initial Release
## 1.0.1 ##
  * Added noNestingClass option, to prevent nesting in some elements.

# Download #

I have compiled a compressed version which has only 5kB:
> [NestedSortable 1.0.1](http://nestedsortables.googlecode.com/files/inestedsortable-1.0.1.pack.js) (You will also need to download the dependencies)

You can also get the whole code, as well as the test code used in the demo:
> [NestedSortables Source 1.0.1](http://nestedsortables.googlecode.com/files/nestedSortables.1.0.1.complete.with.tests.zip)

# Dependencies #

It was built on the latest versions of both jQuery and Interface.

  * [jQuery](http://jqueryjs.googlecode.com/files/jquery-1.1.4.pack.js) 1.1.4 (currently with jQuery 1.2 there is an [IE-specific  problem](http://code.google.com/p/nestedsortables/issues/detail?id=4) caused by Interface incompatibility - thanks to n3dst4 for reporting it, so I recomend sticking to 1.1)
  * [Interface 1.2](http://interface.eyecon.ro/interface/interface_1.2.zip) or above (only needs the components bellow, in that order)
    * iutil.js
    * idrag.js
    * idrop.js
    * isortables.js

Those dependencies have to be loaded in your HTML file in that order and before the NestedSortable plugin itself.

# Usage #

## Basic Usage ##

Include the dependencies and the plugin itself in your HTML file.

```
<script type="text/javascript" src="jquery.js"></script>
<script type="text/javascript" src="interface.js"></script>
<script type="text/javascript" src="inestedsortable.js"></script>
```

Include the HTML that represents your nested list. I recommend you to use an UL with LIs to represent the list, but you can actually use any pair of HTML elements that can be nested. The elements that are being sorted (the ones with the "accept" class) should have an ID, so the list can be serialized and sent back to the server.

Be aware than when a new nesting is created (eg. you make an element a child of a childless element), the plugin will create a new HTML element of the same type of the container element (in this example an UL) with all CSS classes this element has, inside the parent element (in this example an LI).

```
<ul id="list-container">
  <li class="sortable-element-class" id="ele-1">
    Element 1
  </li>
  <li class="sortable-element-class" id="ele-2">
    Element 2
  </li>
  <li class="sortable-element-class" id="ele-3">
    Element 3
    <ul>
      <li class="sortable-element-class" id="ele-4">
        Element 4
        <ul>
          <li class="sortable-element-class" id="ele-5">
            Element 5
          </li>
        </ul>
      </li>
    </ul>
  </li>
</ul>
```

Add the JavaScript code that will set up your NestedSortable list. The minimum configuration options are used bellow, but you probably should want to add more parameters, such as an onChange callback (read the next section).

```
jQuery( function($) {
$('#list-container').NestedSortable(
  {
    accept: 'sortable-element-class',
  }
);
});
```

## Configuration Parameters ##

The NestedSortable plugin will add two functions to the jQuery object: `NestedSortable` and `NestedSortableDestroy`. The first will configure the nested sortable and takes a object of configuration parameters, the second will undo such configuration and takes no parameters.

`NestedSortable` needs to be called on a jQuery object after selecting the elements that will be the list containers. If you have only one list you should probably use `$.NestedSortable("#element_id")` to select it.

The table bellow shows all the parameters that can be passed to the `NestedSortable`. These parameters are passed as a JS object. Use the `{}` notation to create such object. Many of those options are inherited from of the original Interface's Sortable.

Native options are exclusive to the NestedSortable plugin. Inherited options came from Interface's Sortable. Not all options from the [original Sortable](http://interface.eyecon.ro/docs/sort) are listed here, the ones missing might work, but are not supported or useful.

| **Parameter Name**  | **Value Type** | **Type / Default** | **Description** |
|:--------------------|:---------------|:-------------------|:----------------|
| `accept` |  String | mandatory | Css class name of the elements that are going to the sorted. They have to be inside the selected list container. |
| `rightToLeft` | Boolean | native / `false` | If you set this options, the indentation will be applied from right to left. Useful for right-to-left reading languages. |
| `nestingPxSpace` | Integer | native / `30` | The indentation of the nested elements, in pixels. An "margin" css style will be applied to the nested list containers. |
| `autoScroll` | Boolean | native / `true` | By default, when you are dragging an element and reach the top or bottom edge of the screen, your browser window scroll automatically to reveal the NestedSortable elements that are not being displayed. |
| `scrollSensitivity` | Integer | native / 20 | How close to the edge you need to be in order for the autoScroll to happen. Only applied when is autoScroll is on. |
| `scrollSpeed` | Integer | native / 20 | How fast will the window be scrolled by autoScroll. Only applied when is autoScroll is on. |
| `serializeRegExp` | RegExp | native / `/[^\-]*$/` | Regular expression that will be used to select only a part of the element id, when using serialization. The default will select only what comes after the last `-`, so if you have elements with ids such as `my-sortable-element-NUMBER`, only `NUMBER` will appear in the serialization, to identify the element that is in a certain position. You usually want to use that to filter only the element DB id. Refer to serialization in the docs. |
| `currentNestingClass` | String | native / "" | Css class that will be applied, only when dragging, to the nesting where the dragged element is currently in. Provides the user with some visual aid to help identify who is going to be the parent of the element being dragged.|
| `noNestingClass` | String | native / "" | Elements that have this CSS class, in addition to the `accept` class, won't receive elements as children (eg.: will only be nodes in the tree).|
| `activeclass` | String | inherited | When an acceptable draggable is moved the droppable (the list container for the NestedSortable) gets this class |
| `hoverclass` | String | inherited | When an acceptable draggable is inside the droppable (the list container for the NestedSortable), the droppable gets this class. |
| `helperclass` | String | inherited | The Helper is used to point the place where the item will be positioned. This is the css class the will be applied to the helper. |
| `opacity` | Float ( < 1) | inherited | Opacity of the item while it is dragged. |
| `ghosting` | Boolean | inherited / false |  When true the sorthelper will contain a copy of the dragged element, while dragging. |
| `fx` | Integer | inherited | When set, this dictates the duration of a special effect (a fade in) applied to the dragged element, once it is released. |
| `revert` |  Boolean | inherited | When set, makes the dragged element fly from its current position to the place it will be moved to. Fx needs to be set for this to work. |
| `handle` | String | inherited | When this is set to the CSS class name of an HTML element that is inside the draggable items, this element will be used as the drag handle, instead of the whole item itself. |
| `cursorAt` | Object | inherited | The dragged element is moved to the cursor position with the offset specified. It is an object with properties for `top`, `left`, `right` and `bottom` offset. |
| `onChange` | Function | inherited | This will be called after a drag and drop operation changed the disposition of the list. An array with the serialized representation of all altered NestedSortables is provided as the first parameter (more than one Nested Sortable might be changed in only one operation, if you move one item from a list to another.). `this` refers to the NestedSortable DOMElement. Refer to the Serialization section in the docs. |
| `onStart` | Function | inherited | Callback function triggered when the dragging starts. `this` refers to the NestedSortable DOMElement. |
| `onStop` | Function | inherited | Callback function triggered when the dragging stops. `this` refers to the NestedSortable DOMElement. |
| `onHover` | Function | inherited | Called when an accepted draggble is hovering a NestedSortable. Gets as parameter the draggable DOMElement. `this` refers to the NestedSortable DOMElement. |
| `onOut` | Function | inherited | Called when an accepted draggable is leaving a NestedSortable. Gets as parameter the draggable DOMElement. `this` refers to the NestedSortable DOMElement. |

## Serialization ##

The support for serialization was built into the NestedSortable in order to be similar in its usage to the original Sortable from Interface. It is not very well documented there, so I will try to explain in briefly here.

The way to do it is to define an onChange callback, that will get called whenever the list is modified. This onChange will receive a parameter, with an array with the serialized representation of all altered NestedSortables since its last call. More than one NestedSortable list might be changed in only one operation, if you move one item from a list to another.

You define the callback using something such as:

```
jQuery( function($) {
$('#list-container').NestedSortable(
  {
    accept: 'sortable-element-class',
    onChange : function(serialized) {
      // Do something with serialized here
      // such as sending it to the server via an AJAX call
      // or storing it in a JS variable that you can
      // send to the server once the user presses a button.
    },
  }
);
});
```

The `serialized` argument is an array of JS objects, one for each NestedSortable that was modified, each with the following format, exactly as in the original Sortable:

```
{
  id: "list-container", //id of the modified NestedSortable
  hash: "list-container[0][id]=2&list-container[1][id]=1(...)", 
            //String representation of the all the elements that
            //belong to this NestedSortable. It is format
            //is compatible with GET or POST requests, so you
            //should probably want to send this to the server.
  o: [Object id=2, Object id=1, (...)] 
            //Exactly the same contents of `hash`, but represented
            // in a JS array. Use that if you need to manipulate the
            // the list before sending it to the server.   
}
```



Due to the nature of the NestedSortable, the contents of `o` and `hash` are a little more complex. In the [original Sortables](http://interface.eyecon.ro/docs/sort), it is basically a flat array with the ids of the elements in the order they are positioned.

The first difference is that, instead of the whole element id, exactly as in the HTML id, the NestedSortable will only write a portion of that id, filtered by a regular expression, thanks to the `serializeRegExp`. If you don't want to mess with this option, simply name your elements with ids in the format `some_pretty-funky-name-LASTNAMEORNUMBER`, where `LASTNAMEORNUMBER` should identify the element in your database, for saving purposes. I added this option because it simplifies a little the job you will have on the server side implementation.

The biggest difference is that, instead of a simple list, we need to represent a tree-like list. To do that, I use a combination of arrays and objects. `o` is an array where each element in an object which may contain the following properties:

  * **id** - the proper id of the element, after being filtered by the RegExp.
  * **children** - an array just like the parent one, containing all the children of this element.

`hash` is basically the same thing in URL query string format. If you pass it to a GET request, your server should be smart enough to generate an "array of objects" (or equivalent) in your server side language.

Instead of being boring and wasteful by providing a formal grammar representing the `hash` format, I will just provide a simple example, which should make it obvious.


```
serialized[0] = {
 o:[
      {
        id:1, 
      },
      {
        id:2, 
        children: 
          [
            {
            id:3, 
            children: [
              {
              id:4, 
              },
              {
              id:5, 
              }
            ]
            }
          ]
      }
    ],
//ignore the white space in the hash property
hash:   "list-container[0][id]=1&
        list-container[1][id]=2
        &list-container[1][children][0][id]=3
        &list-container[1][children][0][children][0][id]=4
        &list-container[1][children][0][children][1][id]=5"
}
```


# Known Bugs #

Currently this plugin has no know bugs of its own. Interface 1.2, however, was built for jQuery 1.1 and does have a few issues, specially with animations, when you use it with jQuery 1.2. You might encounter issues with the animation options in the NestedSortables, which are inherited from Interface. jQuery 1.2 was just released, lets hope Interface is updated to support it. The combination used in the demo is using jQuery 1.2 and Interface 1.2, and at least in those examples, doesn't generate any errors.

# TODO for next versions #

  * Provide an option to limit the levels of nesting that can be used







