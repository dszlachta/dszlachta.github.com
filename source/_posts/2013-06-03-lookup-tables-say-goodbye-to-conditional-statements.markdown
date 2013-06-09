---
layout: post
title: "Lookup Tables: Say Goodbye to Conditional Statements"
date: 2013-06-03 20:54
comments: true
categories: javascript
---

Have you ever wonder how to get rid of all those `if`s and `switch`es in your
code? Well, it's possible thanks to the technique known as **Lookup tables**.

It's simple. If you're using conditional statements to check for some numbers
(or things that can be represented by numbers), you can populate an array with
values (like functions) you need, instead.

<!-- more -->
``` javascript
    function getName(id) {

        switch (id) {
            case 0:
                return 'Ann';
                break;
            case 1:
                return 'John';
                break;
            case 2:
                return 'Kate';
                break;
        }

    }
```
Can be simplified to: 

``` javascript
    var names = [ 'Ann', 'John', 'Kate' ];

    function getName(id) {
        return names[id];
    }
```

Let's think about some more complicated example, like capturing the keys user
presses. Normally, you need a huge `switch` statement, don't you?

First, let's prepare our playground:

``` javascript
    var Keys = window.Keys = {
        
        /* our lookup table */
        bindings: [],

        /* helper function; binds callback to item in the lookup table */
        bind: function(code, fn) {

            this.bindings[code] = fn;

        },

        /* this returns callback from the lookup table */
        perform: function(code) {

            return this.bindings[code];

        }

    }
```

Looks fine, but what if there's no such key code in the table? Let's add an
empty function `defaultAction` to the `Keys` object:
    
``` javascript
    defaultAction: function() {},
```    

We have to modify `Keys.perform` to return binded function or
`defaultAction`:

``` javascript
    perform: function(code) {

        /* return function binded to key code. If there's no function
         * binded, return default function 
         */
        return this.bindings[code] || this.defaultAction;

    }
```

That's all. Now we can bind some keys and functions and perform some actions
like this:

``` javascript
    Keys.bind('o'.toUpperCase().charCodeAt(0), function() {
        console.log('You pressed o!');
    });

    window.onkeydown = function(event) {
        
        Keys.perform(event.keyCode).call(this, null);

    };
```

I've made a demo of simple
script that, when user presses a key, adds a letter to a `div` element. If letter
matches any letter of world *google*, script will put this letter in the same
colour as in Google's logo.

See code here: https://github.com/dszlachta/demos/googlefier (note how I coded inserting *o* in different colours without usign any `if`
statements :) )

Why lookup tables are better than big `switch` statements? Code is simplier
(and that means it's better) and you get a performance boost (the JS engine
don't have to check values). You can read more about lookup tables in Nicholas
C. Zakas' book *High Performance JavaScript*.
