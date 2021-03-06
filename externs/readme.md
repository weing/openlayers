# Externs

This directory contains externs files, which tell the Closure compiler about symbols and properties that it should not rename.

## oli.js

These are special externs that belong to OpenLayers, and this document explains their purpose and how they are used.

### Prevent class properties from being renamed

For events, we make properties available to the application. Methods can be made available by just marking them with the `@api` annotation directly where they are defined; properties should also be added to `oli.js`:

```js
/**
 * @interface
 */
oli.MapBrowserEvent = function() {};

/**
 * @type {ol.Coordinate}
 */
oli.MapBrowserEvent.prototype.coordinate;
```
In the source file (`src/ol/MapBrowserEvent.js`), the class needs to implement this interface:
```js
/**
 * ...
 * @constructor
 * @implements {oli.MapBrowserEvent}
 */
ol.MapBrowserEvent = function(type, map, originalEvent, opt_frameState) {

  // ...

  /**
   * @type {ol.Coordinate}
   * @api
   */
  this.coordinate = map.getEventCoordinate(this.originalEvent);

  // ...

};
```

### Override methods in custom classes

For custom subclasses in applications, which can be created using `ol.inherits`, the API may want to make certain methods available to override. In addition to marking such methods as `@api`, they should also be added to an interface in `oli.js`:
```js
/**
 * @interface
 */
oli.control.Control = function() {};

/**
 * @param {ol.PluggableMap} map Map.
 * @return {undefined} Undefined.
 */
oli.control.Control.prototype.setMap = function(map) {};

```
This interface must be implemented by the class in the source file (`src/ol/control/control.js`):
```js
/**
 * ...
 * @constructor
 * @implements {oli.control.Control}
 */
ol.control.Control = function(options) {
  // ...
};

// ...

/**
 * Application subclasses may override this.
 * @param {ol.PluggableMap} map Map.
 * @api
 */
ol.control.Control.prototype.setMap = function(map) {
  // ...
};
```
See Custom controls example for an example of how this can be used.
