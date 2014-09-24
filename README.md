## Mojo Models [![Build Status](https://travis-ci.org/classdojo/mojo-models.svg)](https://travis-ci.org/classdojo/mojo-models)

### Installation

```
npm install mojo-models
```

### Features

- easy to extend. register your own custom plugins to extend the functionality of models.

## API

### Base(properties[, application])

Inherits [bindable.Object](https://github.com/mojo-js/bindable.js)

base model constructor

- `properties` - properties to set on the model
- `application` - (optional) mojo application

```javascript
var models = require("mojo-models");
var model = new models.Base({ message: "Hello world!" });
console.log(model.message);
```

#### base.data

The raw data set on the model - this is usually transformed into something the model can 
use via `deserialize`. 

```javascript
var model = new models.Base({ data: { message: "Hello world!" }});
consol.log(model.message); // Hello world!
console.log(model.data); // { message: "Hello world!" }
```

#### base.deserialize(data)

deserializes data once `data` is set on the model

```javascript


var Person = models.Base.extend({
  deserialize: (data) {
    return {
      firstName: data.firstName,
      lastName: data.lastName,
      fullName: data.firstName + " " + data.lastName
    };
  }
});

var person = new Person({
  data: {
    firstName: "Craig",
    lastName: "Condon"
  }
});

console.log(person.fullName); // Craig Condon

person.set("data", { 
  firstName: "A",
  lastName: "B"
});

console.log(person.fullName); // A B
```

#### base.serialize()

serializes data. This is an alias to `toJSON`

### Collection(properties[, application])

Inherits [bindable.Collection](https://github.com/mojo-js/bindable.js)

#### model collection.createModel(options)

creates a model

#### model collection.create(options)

creates a new model, and adds to the collection immediately


## Built-in plugins

#### persist

#### virtuals

Virtual properties all you to load external resources as they're needed. This is especially useful when
data-binding models to views.


```javascript

var superagent = require("superagent");

var Friends = models.Collection.extend({
  createModel: function (options) {
    return new Person(options, this.application);
  },
  persist: {
    load: function (complete) {
      superagent.get("/person/" + this.friendee._id + "/friends", funtion (err, result) {
        complete(null, result);
      });  
    }
  }
});

var Person = models.Base.extend({
  virtuals: {
    friends: function (onLoad) {
      new Friends({ friendee: this }).load(onLoad);
    }
  }
});

var person = new Person({ _id: "person1" });

console.log(person.get("friends")); // should be undefined

// activates virtual property
person.bind("friends", function (friends) {
  this.dispose(); // dispose the binding immediately
});

```

#### bindings

Bindings allow you to compute properties on models.

```javascript

var bindable = require("bindable");

var Person = models.Base.extend({
  bindings: {
    "firstName, lastName": function (firstName, lastName) {
      this.set("fullName", firstName + " "+ lastName);
    }
  }

var person = new Person({ firstName: "A", lastName: "B" });
console.log(person.fullName); // 
document.body.appendChild(person.render());
```


## Application API
