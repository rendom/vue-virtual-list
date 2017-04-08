# vue-virtual-list

DynamicHeight is broken atm.  

### 1. Install component
`npm install vue-virtual-list`

If you have mixed heights on your rows then you need to specify `h (Number)` property on your object with the height.

### 2. Setup your app
```
<template>
  <div id="app">
    <vue-virtual-list>
        <div v-for="item of items">{{item.name}}</div> 
    </vue-virtual-list>
  </div>
</template>

<script>
import vl from 'vue-virtual-list'

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
    }
  }
}
</script>
```

### Component properties
| Name        | Type          | Default  |
| ------------- |:-------------:| -----:|
| scroll      | String      |   None, this prop is required can either be `window` or `container`. |
| scrollContainerHegiht | Number      |    Defaults to 500 and is only required if you use scroll container |
| dynamicHeight | Boolean      |    Defaults to `true` this requires that you specify height on your objects. |
| rowHeight | Number      |    Defaults to 0 and is only required if you set objectHeight to false|
