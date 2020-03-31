On-screen keyboard detector
=============================
> Detects the presence of the on-screen keyboard (OSK) shown by mobile browsers when the user interacts with input controls on a webpage.

Background
----------
This approach employs the browsers layout and visual viewports ([Article on MDN][1], [Demo][2]) to 
observe the appearance of the virtual keyboard. 

At the time of writing on 
- *Mobile Safari* the keyboard is excluded from the visual viewport, while on
- *Chrome for Android* the keyboard is excluded from both the visual and the layout viewport.

TODO: small sketch of the viewports with the OSK

*Chrome's* behaviour makes it necessary to also observe `focusin`, `focusout`, `resize` and `visibilitychange` events. 

Caveats
------
- On *Chrome for Android* the keyboard must be *initially hidden* when subscribing to the detector.
- On *Chrome for Android* the `hidden` and `visible` events are dispatched with a approximate 1 second delay.

Usage
-----
### Basic
```javascript
import oskd from 'on-screen-keyboard-detector';

const unsubscribe = oskd(visibility => {
	if (visibility === "hidden"){
		// ...
	}
	else { // visibility === "visible"
		// ...
	}
});

// After calling unsubscribe() the callback will no longer be invoked.
unsubscribe();
```

API
---
### oskd(listenerCallback)
Begins to observe browser events and invokes the provided callback function
when a change in the keyboard visibility is detected.

TODO: mention the parameters and the return value

Advanced Usage
--------------
### Multiple Subscriptions (PubSub)
PubSub is not part of this module and needs additional tools, e.g. [emittery][3]. See [`demo/pubsub.html`](./demo/pubsub.html)
```javascript
import oskd from 'on-screen-keyboard-detector';
import Emitter from 'emittery';

const emitter = new Emitter();

oskd(visibility => emitter.emit(visibility));

emitter.on('hidden', function() { /* ... */ });
emitter.on('visible', function() { /* ... */ });
```

Tests
-----
#### Requirements (not listed in `package.json`)
- mocha :coffee:
- chai :tea:
- selenium-webdriver
- a Mac for Mobile Safari tests
- running a local webserver (see `TEST_SERVER` in `package.json`)

### Android
For real devices make sure the adb server is running (`adb start-server`) and a device is connected via USB or Wifi  (`adb devices -l`)
`npm run test:chrome`

### iOS
Connect a device where `Remote Automatation` is enabled for Safari (see the [Webkit blog][4]).

**iOS tests should be performed manually (see the [demo](./demo) folder), because Webdriver controlled Mobile Safari does not show the virtual keyboard**



[1]: https://developer.mozilla.org/en-US/docs/Web/API/Visual_Viewport_API
[2]: http://bokand.github.io/viewport/index.html
[3]: https://github.com/sindresorhus/emittery
[4]: https://webkit.org/blog/9395/webdriver-is-coming-to-safari-in-ios-13/