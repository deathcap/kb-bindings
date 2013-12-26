# kb-bindings

Expose a polling object and edge-triggered events for (game) keybindings using [vkey](https://github.com/chrisdickinson/vkey/) definitions.

Based on @chrisdickinson's [kb-controls](https://github.com/chrisdickinson/kb-controls) (should be mostly compatible)

```javascript
var kb = require('./index')
  , raf = require('raf')

var ctl = kb({
  '<left>': 'strafe_left'
, '<right>': 'strafe_right'
, '<up>': 'forward'
, '<down>': 'backward'
, 'W': 'forward'
, 'A': 'strafe_left'
, 'S': 'backward'
, 'D': 'strafe_right'
, '<mouse 1>': 'fire'
, 'E': 'inventory'
})

raf(document.body).on('data', function(dt) {
  console.log(!!ctl.forward)
})

```

### Why not events?

Events are great! I love them. But when you're writing game logic, oftentimes you want the frame event to drive the simulation -- and dealing with the keyboard as a separate evented interface can be troublesome in this regard.

This module provides both a polling object and up/down events for each binding, so you
can use which ever is most convenient for your application (level-triggered or edge-triggered).

## API

#### kb = require('kb-bindings')

return the `kb` function.

#### ctl = kb([DOMElement,] bindings[, augmentObject, opts])

Add event listeners to `DOMElement` or `document.body` if not provided.

Bindings is a map of `vkey`'s to desired property names:

```javascript
// bindings example
{ 'X': 'do_something'
, '<space>': 'jump'
, '<control>': 'sprint' }

// would yield the following ctl object (sans methods):
{ 'do_something': 0
, 'jump': 0
, 'sprint': 0 }
```

If `augmentObject` is passed, these property names will be attached to it instead
of a new object.

If `opts` is passed, the following options are understood:

preventDefaults: if true (default), the default browser action for the DOM events will be prevented

#### ctl[yourPropertyName] -> Number

If the number is truthy, that means it's actively being pressed. Otherwise it's not. If it's
greater than 1, then two different keys may have been bound to the action and are simultaneously being pressed.

#### ctl.enable()

Enables the keyup, keydown, mouseup, mousedown, and contextmenu listeners (and makes them `preventDefault()` if `preventDefaults` is set.)

#### ctl.enabled() -> boolean

Returns whether or not the `ctl` is enabled.

#### ctl.disable()

Disables the DOM listeners (without removing them). Keyboard and mouse events should work
as normal while the `ctl` is disabled.

#### ctl.destroy()

Removes all DOM event listeners and renders the `ctl` inert.

#### ctl.down.on(binding, function(){})

Emits when the binding state changes to down. Note this event is only emitted when the
state changes (unlike the DOM keydown event it does _not_ continuously emit as the key
is held down).

The event name is the binding, for example:

    ctl.down.on('inventory', function() { ... });

#### ctl.up.on(binding, function(){})

Emits when the binding state changes to up.

## License

MIT


