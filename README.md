# vue-virtual-list


### 1. Install component
`npm install vue-virtual-list`

### 2. Setup row component
```
<template>
  <div v-bind:class="{item: true}">
    {{item.name}} {{item.h}}px
  </div>
</template>

<script>
export default {
  props: {
    item: Object
  }
}
</script>
<style>
  .item {
    height: 20px;
    background: #c6c6c6;
  }
</style>
```
If you have mixed heights on your rows then you need to specify `h (Number)` property on your object with the height.

### 3. Setup your app
```
<template>
  <div id="app">
    <vue-virtual-list :items="items" :childComponent="childComponent"></vue-virtual-list>
  </div>
</template>

<script>
import VueVirtualList from 'vue-virtual-list'
import rowComponent from './components/rowComponent'

export default {
  components: {
    vl
  },
  data () {
    var items = []
    for (var i = 0; i < 50; i++) {
      items.push({name: 'item' + i, h: 20})
    }

    return {
      items: items,
      childComponent: rowComponent,
    }
  }
}
</script>
```

### Component properties
| Name        | Type          | Default  |
| ------------- |:-------------:| -----:|
| items      | Array | []|
| scroll      | String      |   None, this prop is required can either be `window` or `container`. |
| scrollContainerHegiht | Number      |    Defaults to 500 and is only required if you use scroll container |
| dynamicHeight | Boolean      |    Defaults to `true` this requires that you specify height on your objects. |
| rowHeight | Number      |    Defaults to 0 and is only required if you set objectHeight to false|
