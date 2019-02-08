---
title: How To Use Vue
---
Before you begin with Quasar, it is a good idea to get acquainted with ES6 and have a fairly good knowledge about how Vue works. ([Quick overview of ES6](https://github.com/lukehoban/es6features) and [ES6 complete list of features](http://es6-features.org/#Constants) -- don't worry, you don't need to understand ALL of ES6). For devs experienced with reactive UIs, the [Vue documentation](https://vuejs.org/v2/guide/) itself takes a half-day at most to read top-to-bottom and will help you understand how Quasar components can be used and configured.

If you are a total beginner to Vue and reactive UI libraries and want a good tutorial, we recommend you take a look at the [Udemy Course - Vue JS 2 - The Complete Guide](https://www.udemy.com/vuejs-2-the-complete-guide/learn/v4/overview).

After reading the Vue documentation, let's clear up some of the most frequently asked questions, like *"How can I use Quasar components, Vue properties, methods and events"*.

## Vue Single File Components (SFC)
You'll be building your Quasar app using `*.vue` files which contain multiple sections: `template` (HTML), `script` (Javascript) and `style` (CSS/Stylus/SASS/SCSS/Less) all in the same file.

```vue
<template>
  <!-- you define your Vue template here -->
</template>

<script>
// This is where your Javascript goes
// to define your Vue component, which
// can be a Layout, a Page or your own
// component used throughout the app.

export default {
  //
}
</script>

<style>
/* This is where your CSS goes */
</style>
```

### CSS preprocessors
For the `<style>` tag, you can also use whatever CSS preprocessor you want. [Stylus](http://stylus-lang.com/) is available out of the box. For SCSS/SASS or LESS, you'll need to install their Webpack loaders (example: `yarn add --dev less-loader` or `npm install --save-dev less-loader`).

After installing the loader you need (remember Stylus is already installed for you), you can specify you want your chosen preprocessor to handle the CSS code you're writing:

```html
<!-- notice lang="stylus" -->
<style lang="stylus">
.some-div
  font-size 15px
</style>
```

In the above example, you would replace `stylus` with the preprocessor you've chosen.

## Using Quasar Directives
Quasar comes with a few custom [Vue Directives](https://vuejs.org/v2/guide/custom-directive.html). These directives can be applied on almost any DOM element or Component.

Example of a Quasar directive:
```html
<div v-ripple>Click Me</div>
```

> Notice how Ripple is used in the HTML template as `v-ripple`. Vue directives are prefixed with `v-`.

In order for you to use any of the directives that Quasar supplies, you first need to tell Quasar you want it embedded. Open `/quasar.conf.js` file and add the following reference:

```js
framework: {
  directives: ['Ripple']
}
```

Let's take another example. We now also want TouchPan and TouchSwipe directives, so we add them too in `/quasar.conf.js`:
```js
framework: {
  directives: ['Ripple', 'TouchPan', 'TouchSwipe']
}
```

Now we can write in your Vue files template:

```html
<div v-touch-pan="handler">...</div>
<div v-touch-swipe="handler">...</div>
<div v-ripple>Click me. I got ripples.</div>
```

## Using Quasar Components
Quasar components have names beginning with "Q" like "QBtn" or "QElementResizeObserver". In order to use them, you need to add a reference to them in `/quasar.conf.js`.

Let's take the following example with a QBtn and QIcon and then we'll see how to embed these components in our app:
```html
<div>
  <q-btn @click="doSomething" label="Do something" />
  <q-icon name="alarm" />
</div>
```

> Notice how QBtn is used in the Vue HTML template as `<q-btn>`. If we'd import QElementResizeObserver, then we'd use it in template as `<q-element-resize-observer>`.

Now on `/quasar.conf.js`, you would add:
```js
framework: {
  components: ['QBtn', 'QIcon']
}
```

## Using Quasar Plugins
Quasar Plugins are features that you can use both in your Vue files as well as outside of them, like Notify, ActionSheet, AppVisibility and so on.

In order to use them, you need to add a reference to them in `/quasar.conf.js`:
```js
framework: {
  plugins: ['Notify', 'ActionSheet']
}
```

Let's take Notify as an example and see how we can then use it. In a Vue file, you'd write something like this:
```html
<template>
  <div>
    <q-btn
      @click="$q.notify('My message')"
      color="primary"
      label="Show a notification"
    />

    <q-btn
      @click="showNotification"
      color="primary"
      label="Show another notification"
    />
  </div>
</template>

<script>
export default {
  methods: {
    showNotification () {
      this.$q.notify('Some other message')
    }
  }
}
</script>
```

> Notice that in the template area we're using `$q.<plugin-name>` and in our script we say `this.$q.<plugin-name>`.

Now let's see an example of Notify being used outside of a Vue file:
```js
import { Notify } from 'quasar'

// ...
Notify.create('My message')
```

### Importing All Components and Directives for Quick Test
Referencing all Quasar Components, Directives and Plugins can be tiresome when you just want to do a quick test. In this case, you can tell Quasar to import them all by editing `/quasar.conf.js` like this:

```js
framework: 'all'
```

::: warning
This **will not** take advantage of tree shaking, causing your bundle to become bloated with unnescesary/unused code. **Not recommended for production**. Use this only for quick testing purposes.
:::

### Self Closing Tags
Some Quasar components do not need you to include HTML content inside of them. In this case, you can use them as self closing tags. One example with QIcon below:

```html
<q-icon name="cloud" />
```

Self-closing means the above template is the equivalent to:

```html
<q-icon name="cloud"></q-icon>
```

Both forms are valid and can be used. It works the same with regular DOM elements:

```html
<div class="col" />
<!-- equivalent to: -->
<div class="col"></div>
```

Some eslint-plugin-vue linting rules actually enforce using the self-closing syntax.

## Handling Vue Properties
Let's take some examples with a bogus Quasar component (we will call it QBogus) that supports the properties below. We will discuss each of the types of Vue properties in the below sections.

| Vue Property | Type | Description |
| --- | --- | --- |
| `infinite` | Boolean | Infinite slides scrolling |
| `size` | String | Thickness of loading bar. |
| `speed` | Number | How fast should loading bar update its value (in milliseconds). |
| `columns` | Object | Object defining columns (see "Columns Definition" below). |
| `offset` | Array | Array with two numbers. Offset on horizontal and vertical (in pixels). |

### Boolean Property
A boolean property means it only accepts a strictly Boolean value. The values will not be cast to Boolean, so you must ensure you are using a true Boolean.

If you are trying to control that property and change it dynamically at runtime, then bind it to a variable in your scope:
```vue
<template>
  <q-bogus :infinite="myInfiniteVariable" />
</template>

<script>
export default {
  data () {
    return {
      myInfiniteVariable: false
    }
  }
}
</script>
```

If, on the other hand, you know this Boolean value is not going to change, you can use the shorthand version of the variable like a component attribute and just specify it. In other words, if you don't bind the variable to a variable in the component's scope as it will always be `true`:
```vue
<template>
  <q-bogus infinite />

  <!--
    the following is perfectly valid,
    but it's a longer version
  -->
  <q-bogus :infinite="true" />
</template>
```

### String Property
As you can imagine, Strings are required as a value for this type of property.
```vue
<template>
  <!--
    direct assignment, no need for
    a variable in our scope
  -->
  <q-bogus size="24px" />

  <!--
    we can also bind it to a variable
    in our scope so we can dynamically
    change it
  -->
  <q-bogus :size="mySize" />
</template>

<script>
export default {
  data () {
    return {
      // notice String as value
      mySize: '16px'
    }
  }
}
</script>
```

### Number Property
```vue
<template>
  <!--
    Case 1. Direct assignment.
    Notice the colon (":") before property name.
  -->
  <q-bogus :speed="50" />

  <!-- Case 2. Assignment through a scope variable -->
  <q-bogus :speed="myNumber" />
</template>

<script>
export default {
  data () {
    return {
      // notice Number as value
      myNumber: 50
    }
  }
}
</script>
```

### Object Property
```vue
<template>
  <!-- Case 1. Direct assignment. -->
  <q-bogus :columns="{key: 'value', anotherKey: 'another value'}" />
  <!-- or a more elegant way for Case 1: -->
  <q-bogus
    :columns="{
      key: 'value',
      anotherKey: 'another value'
    }"
  />

  <!-- Case 2. Assignment through a scope variable -->
  <q-bogus :columns="myColumns" />
</template>

<script>
export default {
  data () {
    return {
      myColumns: {
        key: 'value',
        anotherKey: 'another value'
      }
    }
  }
}
</script>
```

### Array Property
```vue
<template>
  <!-- Case 1. Direct assignment. -->
  <q-bogus :offset="[10, 20]" />

  <!-- Case 2. Assignment through a scope variable -->
  <q-bogus :offset="myOffset" />
</template>

<script>
export default {
  data () {
    return {
      myOffset: [10, 20]
    }
  }
}
</script>
```

## Handling Vue Methods
You will notice throughout the documentation that some Quasar components have methods that can be called. Example:

| Vue Method | Description |
| --- | --- |
| `next()` | Goes to next slide. |
| `previous(doneFn)` | Goes to previous slide. |
| `toggleFullscreen()` | Toggles fullscreen mode. |

In order for you to access these methods, you will need to set a Vue reference on the component first. Here's an example:

```vue
<template>
  <!--
    Notice ref="myRef". We will use the name
    assigned to "ref" in the script part below
  -->
  <q-bogus ref="myRef" />
</template>

<script>
export default {
  // we can now access `this.$refs.myRef`
  // an example on the mounted() Vue component hook
  mounted () {
    // calling "next()" method:
    this.$refs.myRef.next()
  }
  // calling before mount point might result in errors
  // as Vue hasn't yet prepared the Vue references
}
</script>
```

## Handling Vue Events
You will notice throughout the documentation that some Quasar components have a section called "Vue Events". **Do not confuse** these Vue events with the [Global Event Bus](/options/global-event-bus) as these two have nothing in common.

Example of "Vue Events":

| Event Name | Description |
| --- | --- |
| `@show` | Triggered right after the Modal is shown. |
| `@hide` | Triggered right after the Modal is hidden. |

In order for you to catch these events, when they are triggered, you will need to add listeners for them on the component itself in the HTML template. Here's an example:

```vue
<template>
  <q-bogus @show="doSomething" @hide="doSomethingElse" />
</template>

<script>
export default {
  methods: {
    doSomething () {
      // this method has been called (in this case)
      // because @show event was triggered by QBogus component
    },
    doSomethingElse () {
      // this method has been called (in this case)
      // because @hide event was triggered by QBogus component
    }
  }
}
</script>
```

There are times when you need to access native DOM events on a Quasar component too, like the native `@click`. Do not confuse native events with the Vue events emitted by the component. They are different things. Let's take an example: let's say we have a component (QBogus) that emits `@show` and `@hide`, but doesn't emit a `@click` event. `@click` being a native DOM event, we can still catch it with the `.native` modifier:

```html
<!-- Notice "@click.native" -->
<q-bogus @click.native="myMethod" />
```