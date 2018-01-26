# vue-component-ruler
vue2, mobile, UI kit, ruler, based viewport 750px

[![NPM downloads](https://img.shields.io/npm/dm/vue-component-ruler.svg?style=flat-square)](https://www.npmjs.com/package/vue-component-ruler)

## Install

#### NPM
Install the package. use with vue `^2.0`

```bash
$ npm i vue-component-ruler -S
```

Register the component

```js
import Vue from 'vue'
import Ruler from 'vue-component-ruler'
import 'vue-component-ruler/dist/ruler.min.css';
```

You may now use the component in your markup

```html
<Ruler @onchange="onChange" ref="Ruler" :range="range"/>
```

## Basic Usage

#### props range [Array]
set the range of ruler, for example: [0, 300]

#### props onchange [Function]
will be triggered when value change

#### if you want to change value actively, use ref
```js
this.$refs.Ruler.doOnValueChange(value)
```
