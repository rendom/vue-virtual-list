# vue-virtual-list

```
<template>
  <div id="app">
    <vl :items="filteredData" :childComponent="childComponent"></vl>
  </div>
</template>

<script>
import vl from './components/virtuallist'
import test from './components/test'

export default {
  components: {
    vl
  },
  data () {
    var items = []
    for (var i = 0; i < 50; i++) {
      items.push({name: 'item' + i, h: 50})
    }

    return {
      items: items,
      childComponent: test,
    }
  }
}
</script>


```
