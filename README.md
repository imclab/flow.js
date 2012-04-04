# flow.js
## Introduction

flow.js is a synchron-asynchron flow control library which runs on node and in browsers.
The functionallity of the library is inspired by node-seq (https://github.com/substack/node-seq) and async (https://github.com/caolan/async). The source is written from scratch.

## Development

flow.js is currently under development. The following features will be integrated within the next days:

* parEach - execute one function in parallel for each item inside a given as array or object


## Features

* sequence flow (seq)
* parallel flow (par)
* end       - breaks the flow from inside an action

## Possible features (if somebody like or need)

* seqEach  - execute sequence for each function given as array
* catch     - adds a catch action, which will handle erros instead of exec action
* autoexec  - automatically execute flow

## Usage

### Create a flow and execute

```javascript

    flow()
       .exec(function (err, results) {
            if (err) {
                // some error inside the flow
            } else {
                // access to results
            }
       });
```
### Add a sequence

#### execute synchron method in sequence

```javascript
    flow()
       .seq(function(){
           // do something synchron
       })
       .exec(function (err, results) {           
       });

```

#### execute synchron method in sequence

```javascript
    flow()
       .seq(function(cb){
           // do something asynchron
           setTimeout(function(){
                // invoke callback
                cb(null, "result of this sequence");
           }, 1000);
       })
       .exec(function (err, results) {
       });

```


#### sequence with variable registration

```javascript
    flow()
       .seq("myVar", function(){
           return "valueOfMyVar";
       })
       .exec(function (err, results) {
           console.log(results.myVar);
       });

```

### Add parallel control flow

#### asynchron and synchron control parallel control flows

```javascript
    flow()
       .par([
            function() {
                // synchron method
            },
            function (cb) {
                // asynchron method
            }
            // and even more
       ])
       .exec(function (err, results) {
       });

```

#### asynchron and synchron control parallel with variable registration

```javascript
    flow()
       .par({
            a: function() {
                // synchron method
                return 123;
            },
            b: function (cb) {
                // asynchron method
                setTimeout(function(){
                    cb(null, "I completed after 100ms");
                }, 100);
            }
            // and even more
       })
       .exec(function (err, results) {
            console.log(results);
            // results.a = 123;
            // results.b = "I completed after 100ms");
       });

```


### end flow from inside an synchron action

```javascript
    flow()
        .seq("a", function() {
            // do something
        }
        .par({
            b: function() {
                // do something synchron in parallel ...

                // end flow on condition
                if (condition) {
                    this.end(); // end flow after return statement
                    return -2;
                }

                return 2;
            },
            c: function(cb) {
                // ... with something asynchron

                // end flow the asynchron way
                this.end(null, 3); // or cb.end(null, 3) or cb(null, 3, true) or this(null, 3, true)
            }
        })
        .seq("e", function() {
            // this would executed, because either b or c will end the flow
        }
        .exec(function(err, results) {
            /* this function is called on
               * error thrown in synchron method
               * error object passed in asynchron method
               * this.end() is called
               * flow is executed completely
            */
        });
```
