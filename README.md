# bacon.model

A data binding plugin for [Bacon.js](https://github.com/baconjs/bacon.js).

Adds `Model`, `Binding` and `Lens` objects to core library to support advanced binding

`Model` object extends the Bacon.js `Property` API by providing a bidirectional binding

Includes

- Composing `Model` objects using `model.bind`, `Model.combine` and `model.lens`
- Attaching additional input `EventStream` to any `Model` by using
  `model.addSource`

## Model API

###Bacon.Model(initValue)

Creates a new model, with the given (optional) initial value.

###model.set(value)

Sets a new value for the model, also pushing this
value to all two-way sources.

###model.get()

Returns the current value of the model. If there's no current value,
returns `undefined`.

###model.modify(f)

Modifies the value of the model by applying the
given function to the current value. For instance, if the current value
was `1` and you applied a `multiplyBy2` function, the value would be set
to `2`.

###model.addSource(stream)

Adds an input source for pushing values to
the model. The source may be an EventStream or a Property. The method
returns an EventStream that contains all changes from *other sources*
than this.

###model.apply(stream)

Adds an input source of *modification functions* to the model. The source may be an EventStream or a Property, and is supposed to contain functions as values. Each of these functions are applied as modifications to the value of the model (as with using the `modify` method). The method returns an EventStream that contains all changes from *other sources* than this.

###model.bind(other)

Makes a two-way binding between the two models.

###model.lens(lens)

Creates a new lensed model based on this one. For example:

```js
    car = Bacon.Model({ brand: "Ford", engine: "V8" })
    engine = car.lens "engine"
```

Now the `engine` model will have the value "V8". Also, these two models
are bound both ways meaning that changes in `engine` are reflected to
`car` and vice versa.

See Lenses section below for full definition of Lenses.

###Bacon.Model.combine(template)

Creates a composite model using a template. For example:

```js
    // Model for the number of cylinders
    cylinders = Bacon.Model(12)
    // Model for the number of doors
    doors = Bacon.Model(2)
    // Composite model for the whole car
    car = Bacon.Model.combine {
      price: "expensive",
      engine: { type: "gas", cylinders},
      doors
    }
```

The composite model has a bidirectional binding to its components. If
the `cylinders` model is gets a change from a UI, the `car` model is
updated accordingly. Also, if you set the value in the `car` model to,
say `{price: "affordable", engine: { type: "electric", cylinders: 0 },
doors: 4}`, the `cylinders` model will get a new value 0.

###model.syncConverter

The model has a `syncConverter` function that it uses to map the
incoming data values from its synchronization sources, i.e. the sources
that have been added using `bind`, `addSource`, or explicitly using
`Model.combine`. You can override this method to process the incoming
values. For instance, you may convert `undefined` values to empty
strings like this:

```js
   model.syncConverter = function(x) { return x || "" }
```

## Binding API

BJQ uses a simple `Binding` API for creating `Model` objects bound to
DOM elements. 

###Bacon.Binding(options)

Creates a new bound `Model`. The `options` argument is an object containing the following fields:

`get` : zero-arg function that returns the current value from the UI

`set` : 1-arg function that pushes the given new value to the UI

`events` : an `EventStream` of input events from the UI. The content of
these events are ignored; they are only used to trigger the polling of
the new value from the UI using the `get` function.

`initValue (optional)` : initial value to be set for the model

## Lenses 

TODO: reference to functional lenses

A lens can be defined in two ways:

### Bacon.Lens(path)

Creates a lens with a p path string, such as `"engine"` or `"engine.cylinders"`

### Bacon.Lens({get, set})

Creates a lens with a `{get, set}` pair such as `{ get: function(context) { .. }, set: function(context, value)
  { .. }}`

TODO: more

## Building

The `bacon.model` module is built using NPM and Grunt.

To build, use `npm install`.

Built javascript files are under the `dist` directory.

## Automatic tests

Use the `npm test` to run all tests.

Tests include mocha tests under the `test` directory

TODO: bower links and travis status