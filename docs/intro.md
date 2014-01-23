<link rel="stylesheet" href="/shepherd/css/shepherd-theme-arrows.css" />
<script src="/shepherd/shepherd.min.js"></script>

## Shepherd

Guide your users through a tour of your app.

### Dependencies

Shepherd uses [Tether](http://github.hubspot.com/tether/docs/welcome) to
position each step container.  Tether is included in the `shepherd.js` and `shepherd.min.js` files.

Otherwise, none!

### Usage

First create a new `Tour` instance for your tour:

```coffeescript
tour = new Shepherd.Tour
  defaults:
    classes: 'shepherd-theme-arrows'
    scrollTo: true
```

The `defaults` option allows you to specify any options which should be applied
to all this tour's steps by default.

Next, add your steps:

```coffeescript
tour.addStep 'example-step',
  text: 'This step is attached to the bottom of the <code>.example-css-selector</code> element.'
  attachTo: '.example-css-selector bottom'
  classes: 'example-step-extra-class'
  buttons: [
    text: 'Next'
    action: tour.next
  ]
```

Finally, to start the tour, just call `start` on your `Tour` instance:

```coffeescript
tour.start()
```

### API

#### Global Shepherd Object

Shepherd exposes a single object onto the window, `Shepherd`.

That global object fires several events to let you link up actions with events
occuring in _any_ tour:

##### Methods

- `Shepherd.on(eventName, handler, [context])`: Bind an event
- `Shepherd.off(eventName, [handler])`: Unbind an event
- `Shepherd.once(eventName, handler, [context])`: Bind just the next instance of an event

##### Events

The global Shepherd fires the following events whenever a `Tour` instance fires them.  It adds to the object passed to the
event handlers a `tour` key pointing to the instance which fired the event:

- `complete`
- `cancel`
- `hide`
- `show`
- `start`

##### Current Tour

The global `Shepherd` includes a property which is always set to the currently active tour, or null if there is no active tour:

- `Shepherd.activeTour`

#### Tour Instances

##### Creation

You create a `Tour` object for each tour you'd like to create.

Tour's constructor accepts a hash of options:

```javascript
myTour = new Shepherd.Tour({ options })
```

##### Tour Options

- `steps`: An array of Step instances to initialize the tour with
- `defaults`: Default options for Steps created through `addStep`

##### Tour Methods

- `addStep(id, options)`: Creates a new Step object with options.  If you'd like you can also just pass an options hash which includes `id` as a key.
You can also pass an existing `Step` instance rather than `options`, but note that Shepherd does not support a Step being attached to multiple Tours.
- `getById(id)`: Return a step with a specific id
- `next()`: Advance to the next step, in the order they were added
- `back()`: Show the previous step, in the order they were added
- `cancel()`: Trigger cancel on the current step, hiding it without advancing
- `hide()`: Hide the current step
- `show([id])`: Show the step specified by id (if it's a string), or index (if it's a number) provided.  Defaults to the first step.
- `start()`: Show the first step and begin the tour
- `getCurrentStep()`: Returns the currently shown step
- `on(eventName, handler, [context])`: Bind an event
- `off(eventName, [handler])`: Unbind an event
- `once(eventName, handler, [context])`: Bind just the next instance of an event

##### Tour Events

- `complete`: Triggered when the last step is advanced
- `cancel`
- `hide`
- `show`: Triggered with a hash of the `step` and the `previous` step
- `start`

Steps are instances of the Step object.  They are generally created by the `Tour::addStep` method, which returns the `Step` instance its
created.

#### Steps

##### Step Options

- `text`: The text in the body of the step.  It can either be an HTML string, or an array of
strings, each of which will become a `<p>` paragraph.
- `title`: The steps title.  It becomes an `h3` at the top of the step.
- `attachTo`: What element the step should be attached to on the page.  It can either be a string of the form
`"element on"`, or an object with those properties.  For example: `".some #element left"`, or `{element: '.some #element',
on: 'left'}`.  If you use the object syntax, `element` can also be a DOM element.  If you don't specify an `attachTo`
the element will appear in the middle of the screen.
- `classes`: Extra classes to add to the step.  `shepherd-theme-arrows` will give you our theme.
- `buttons`: An array of buttons to add to the step.  By default we add a Next button which triggers `next()`, set this to false
to disable.  Each button in the array is an object of the format:
  - `text`: The HTML text of the button
  - `classes`: Extra classes to apply to the `<a>`
  - `action`: A function executed when the button is clicked on
  - `events`: A hash of events to bind onto the button, for example `{'mouseover': function(){}}`.  Adding a click event to `events` when you
  already have an `action` specified is not supported.
- `advanceOn`: An action which should advance shepherd to the next step.  It can be of the form `"event selector"`, or an object with those
properies.  For example: `"click .some-element"`, or `{element: '.some-element', event: 'click'}`.  You can also always manually advance the
Tour by calling `myTour.next()`.
- `scrollTo`: Should the element be scrolled to when this step is shown?

##### Step Methods

- `show()`: Show this step
- `hide()`: Hide this step
- `cancel()`: Hide this step and trigger the `cancel` event
- `complete()`: Hide this step and trigger the `complete` event
- `scrollTo()`: Scroll to this step's element
- `isOpen()`: Returns true if the step is currently shown
- `destroy()`: Remove the element
- `on(eventName, handler, [context])`: Bind an event
- `off(eventName, [handler])`: Unbind an event
- `once(eventName, handler, [context])`: Bind just the next instance of an event

##### Step Events

- `show`
- `hide`
- `complete`
- `cancel`
- `destroy`

Please note that `complete` and `cancel` are only ever triggered if you call the associated methods in your code.

### Advancing on Actions

You can use the `advanceOn` option, or the Next button, to advance steps.  If you would like however to have a step advance on a
complex user action, you can do the following:

```coffeescript
myStep = myTour.addStep 'my-step', options

yourApp.on 'some-event', ->
  if myStep.isOpen()
    Shepherd.activeTour.next()
```

### Browser Support

IE9+ and all modern browsers