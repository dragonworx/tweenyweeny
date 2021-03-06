# Tweeny Weeny 🤏

A weeeally tweeny simple tweening library based on [EventEmitter3](https://www.npmjs.com/package/eventemitter3).

Features:

- Full set of [easing](https://easings.net) functions available _(including `linear`)_
- Stop a tween during execution
- `"start"`, `"stop"`, `"update"`, `"complete"` events available for full lifecycle awareness using `EventEmitter3` [API](https://nodejs.org/api/events.html)
- Re-usable `Tween` instances. Instance keeps last known value persistent and can be re-started

> Works in the Browser or Node. For the browser `requestAnimationFrame` is used, where as in Node `setTimeout(0)` is used. Frames are generated at the fastest rate available over the desired duration.

## Install

Install as dependency through npm.

`npm install tweenyweeny`

## Usage

Simply create a new `Tween` object _(optionally passing the desired duration in milliseconds, default is `150ms`)_.

```javascript
import { Tween } from "tweenyweeny";

const FROM = 10;
const TO = 20;
const DURATION = 1000; // 1 second
const EASE_FUNCTION = "easeOutQuad";

/* create an instance of Tween, re-use for future animations on same property if required */
const tween = new Tween(DURATION);

/* listen for updates */
tween.on("update", (currentValue) => {
  myThing.theProp = currentValue;
});

tween.on("complete", () => {
  /* animation done */
});

/* start animation */
tween.start(FROM, TO, EASE_FUNCTION);

/* or use the static method */
Tween.run(
  (currentValue) => {
    myThing.theProp = currentValue;
  },
  FROM,
  TO,
  DURATION,
  EASE_FUNCTION
);
```

## Instance API

A `Tween` is an `EventEmitter3` object, so all of that [API](https://nodejs.org/api/events.html) is available to listen to events.

| Member                       | Parameters                                                                                                                                                                                                                                                | Return Value    | Description                                                                                                                                                                                                  |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `new Tween([duration])`      | \* **(optional)** `duration: number` - The animation duration in milliseconds (defaults to 150ms)                                                                                                                                                         | `Tween`         | Create a new instance of `Tween`with the optional duration in milliseconds.                                                                                                                                  |
| `.start(from, to[, easing])` | \* `from: number` - The initial value to start the animation from <br/> \* `to: number` - The value to end the animation with <br/> \* **(optional)** `easing: EasingFunctionName` - The name of the easing function to use _(defaults to `easeOutQuad`)_ | `Promise<void>` | Start the animation interpolating between the `from` and `to` values using the optional easing function name, return a promise.<br/><br/>You can also bind to the `complete` event to be notified when done. |
| `.stop()`                    |                                                                                                                                                                                                                                                           | `void`          | Stop the animation at the current value.                                                                                                                                                                     |
| `.onStart(callback)`         | \* `callback: () => void` - The callback to bind the `start` event to                                                                                                                                                                                     | `Tween`         | Shortcut for `.on('start', callback)` to bind a callback to the `start` event.                                                                                                                               |
| `.onUpdate(callback)`        | \* `callback: () => void` - The callback to bind the `update` event to                                                                                                                                                                                    | `Tween`         | Shortcut for `.on('update', callback)` to bind a callback to the `update` event.                                                                                                                             |
| `.onStop(callback)`          | \* `callback: () => void` - The callback to bind the `stop` event to                                                                                                                                                                                      | `Tween`         | Shortcut for `.on('stop', callback)` to bind a callback to the `stop` event.                                                                                                                                 |
| `.onComplete(callback)`      | \* `callback: () => void` - The callback to bind the `complete` event to                                                                                                                                                                                  | `Tween`         | Shortcut for `.on('complete', callback)` to bind a callback to the `complete` event.                                                                                                                         |
| `.isRunning`                 |                                                                                                                                                                                                                                                           | `boolean`       | Whether the tween is currently running                                                                                                                                                                       |
| `.currentValue`              |                                                                                                                                                                                                                                                           | `number`        | The last computed value for the tween. This will persist after the animation completes.                                                                                                                      |

## Events

| Event        | Callback                             | Description                                                                                                                                     |
| ------------ | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `"start"`    | `(from: number, to: number) => void` | Fired when the Tween is started with `.start()`. Passes the `from` and `to` values if needed.                                                   |
| `"update"`   | `(currentValue: number) => void`     | Fired during each frame of the Tween during animation. Passes the current value.                                                                |
| `"stop"`     | `(currentValue: number) => void`     | Fired when the Tween is stopped with `.stop()` or when `.start()` is called while a tween is running. Passes the current value value if needed. |
| `"complete"` | `(from: number, to: number) => void` | Fired when the animation completes after calling `.start()`. Passes the `from` and `to` values if needed.                                       |

## Static API

| Member                                                | Parameters                                                                                                                                                                                                                                                                                                                                                                                                                                        | Return Value    | Description                                                                                |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- | ------------------------------------------------------------------------------------------ |
| `Tween.run(callback, from, to[, duration, [easing]])` | \* `callback: (value: number) => void` - The update function to call each frame with the current value <br/> \* `from: number` - The initial value to start the animation from <br/> \* `to: number` - The value to end the animation with <br/> \* **(optional)** `duration: number` - The duration in milliseconds<br/> \* **(optional)** `easing: EasingFunctionName` - The name of the easing function to use _(defaults to `"easeOutQuad"`)_ | `Promise<void>` | Create a new `Tween` with the given settings and start the animation, returning a promise. |
| `Tween.defaultDurationMs`                             |                                                                                                                                                                                                                                                                                                                                                                                                                                                   | `number`        | The default duration to use for new instances.                                             |

## Easing Function Names

The following easing function names are available (see [https://easings.net](https://easings.net) for demos):

- `"linear"`
- `"easeInSine"`
- `"easeOutSine"`
- `"easeInOutSine"`
- `"easeInQuad"`
- `"easeOutQuad"` _(default)_
- `"easeInOutQuad"`
- `"easeInCubic"`
- `"easeOutCubic"`
- `"easeInOutCubic"`
- `"easeInQuart"`
- `"easeOutQuart"`
- `"easeInOutQuart"`
- `"easeInQuint"`
- `"easeOutQuint"`
- `"easeInOutQuint"`
- `"easeInExpo"`
- `"easeOutExpo"`
- `"easeInOutExpo"`
- `"easeInCirc"`
- `"easeOutCirc"`
- `"easeInOutCirc"`
- `"easeInBack"`
- `"easeOutBack"`
- `"easeInOutBack"`
- `"easeInElastic"`
- `"easeOutElastic"`
- `"easeInOutElastic"`
- `"easeInBounce"`
- `"easeOutBounce"`
- `"easeInOutBounce"`

# Issues

Please report any issues, feedback, bugs or suggestions [here](https://github.com/dragonworx/tweenyweeny/issues).
