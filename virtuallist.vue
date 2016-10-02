<template>
  <div style="height: 100%">
    <div v-bind:style="{height: scrollHeight +'px', position:'relative'}">
      <ul v-bind:style="{
        'transform': 'translate(0px, '+scrollOffset + 'px)',
        'list-style-type': 'none',
        'padding-left': '0px'
      }">
        <component v-bind:is="childComponent" v-for="item in viewportItems" :item="item" ref="item">
        </component>
      </ul>
    </div>
  </div>
</template>

<script>
var cache = []
// height: 500px; width: 60%; border: 1px solid red; overflow:scroll
export default {
  props: {
    items: {
      type: Array,
      default: []
    },
    childComponent: {
      type: Object
    }
  },
  data () {
    return {
      firstItemIdx: 0,
      lastItemIdx: 52,
      bufferCount: 100,
      scrollTimer: null,
      scrollOffset: 0,
      container: window,

      itemLength: 0,

      containerHeight: 0,
      totalRowsHeight: 0,
      rowHeight: 52,
      dynamicHeight: true,
      scrollHeight: 0,
      viewportItems: []
    }
  },

  mounted () {
    this.itemLength = this.items.length
    this.containerHeight = this.$el.offsetHeight
    if (this.dynamicHeight === false) {
      this.scrollHeight = this.items.length * this.rowHeight
    } else {
      let size = 0
      for (let item of this.items) {
        size += item.h
      }

      this.scrollHeight = size
    }
    this.setViewport()

    this.container.addEventListener('scroll', this.onScroll)
  },

  watch: {
    'items': function () {
      if (this.itemLength !== this.items.length) {
        cache = []
        this.itemLength = this.items.length
        let size = 0
        for (let item of this.items) {
          size += item.h
        }

        this.scrollHeight = size
        this.setViewport()
      }
    }
  },

  methods: {
    onScroll () {
      let self = this
      var top = 0
      var bottom = 0
      if (this.container === window) {
        top = this.container.pageYOffset
        bottom = this.container.pageYOffset + document.documentElement.clientHeight
      } else {
        top = this.container.scrollTop
        bottom = this.container.scrollTop + self.containerHeight
      }

      if (this.dynamicHeight === false) {
        const firstItem = Math.max(0, Math.floor(top / this.rowHeight)) // - self.bufferCount)
        const lastItem = (Math.ceil(bottom / this.rowHeight) - 1) // + self.bufferCount) - 1

        self.firstItemIdx = firstItem
        self.lastItemIdx = lastItem
      } else {
        for (let i = 0; i < self.items.length; i++) {
          const itemPos = self.getScrollOffset(i) + this.items[i].h
          if (itemPos >= top) {
            self.firstItemIdx = i
            break
          }
        }

        for (let i = self.firstItemIdx; i < self.items.length; i++) {
          const itemPos = self.getScrollOffset(i)
          if (itemPos >= bottom) {
            self.lastItemIdx = i
            break
          }
        }
      }
      self.scrollOffset = self.getScrollOffset(self.firstItemIdx)
      this.setViewport()
    },

    setViewport () {
      this.viewportItems = this.items.slice(
        this.firstItemIdx, this.lastItemIdx
      )
    },

    // https://github.com/orgsync/react-list/blob/master/react-list.es6#L309
    getScrollOffset (index) {
      if (cache[index]) {
        return cache[index]
      }

      if (this.dynamicHeight === false) {
        cache[index] = index * this.rowHeight
      } else {
        let from = index
        while (from > 0 && cache[--from] == null);

        let space = cache[from] || 0

        for (let i = from; i < index; ++i) {
          cache[i] = space
          const itemSize = this.items[i].h
          if (itemSize == null) break
          space += itemSize
        }

        cache[index] = space
      }
      return cache[index]
    },

    getHeight (index) {
      if (this.dynamicHeight === false) {
        return this.rowHeight
      } else {
        return this.items[index].item.h
      }
    }

  }
}
</script>
