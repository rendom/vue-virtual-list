<template>
  <div :style="containerStyles">
    <div v-bind:style="{height: scrollHeight +'px', position:'relative'}">
      <div v-bind:style="{
        'transform': 'translate(0px, '+scrollOffset + 'px)',
        'list-style-type': 'none',
        'padding-left': '0px',
        'margin': '0px'
      }">
      </div>
    </div>
  </div>
</template>

<script>
var cache = []

const SCROLL_WINDOW = 'window'
const SCROLL_CONTAINER = 'container'

export default {
  props: {
    scroll: {
      type: String
    },
    scrollContainerHeight: {
      type: Number
    },
    dynamicHeight: {
      type: Boolean,
      default: true
    },
    rowHeight: {
      type: Number,
      default: 0
    },
    bufferCount: {
      type: Number,
      default: 20
    }
  },

    render (createElement) {
        return createElement('div', {
            class: 'containerStyles',
        }, [
            createElement('div', {
                style: {
                    height: this.scrollHeight+'px',
                    position: 'relative'
                }
            }, [createElement('div', {
                style: {
                    transform: `translate(0px, ${this.scrollOffset}px)`,
                    padding: '0px',
                    margin: '0px'
                }
            }, this.getViewport())])
        ]);
    },

  data () {
    return {
      scrollOffset: 0,
        first: 0,
        last: 0,
      //scrollHeight: 0,
      slotsLength: 0,
      containerHeight: 0,
      container: window,
      viewportItems: []
    }
  },

  mounted () {
    this.slotsLength = this.$slots.default ? this.$slots.default.length : 0;

    switch (this.scroll) {
      case SCROLL_CONTAINER:
        this.container = this.$el
        break
      case SCROLL_WINDOW:
      default:
        this.container = window
    }

    this.onScroll()
    this.container.addEventListener('scroll', this.onScroll)
  },

  computed: {
    scrollHeight () {
        //if (this.itemLength !== this.items.length) {
        cache = []
        let size = 0
        if(this.$slots.default) {
            if(this.dynamicHeight) {
                for (let item of this.$slots.default) {
                    if(item.elm) {
                        size += item.elm.clientHeight;
                    }
                }
            } else {
                size = this.slotsLength * this.rowHeight
            }
        }

        return size;
    },

    containerStyles () {
      let styles = {}

      switch (this.scroll) {
        case SCROLL_CONTAINER:
          let height = this.scrollContainerHeight || 500
          styles.height = height + 'px'
          styles.overflow = 'auto'
          break
        case SCROLL_WINDOW:
          styles.height = '100%'
          break
      }

      return styles
    }
  },

  methods: {
    onScroll () {
      let self = this
      var top = 0
      var bottom = 0

      let firstItemIdx = false
      let lastItemIdx = false

      if (this.container === window) {
        top = this.$el.scrollTop - this.$el.getBoundingClientRect().top;
        bottom = this.container.pageYOffset + document.documentElement.clientHeight;
      } else {
        top = this.container.scrollTop
        bottom = this.container.scrollTop + self.containerHeight
      }

      if (this.dynamicHeight === false) {
        firstItemIdx = Math.max(0, Math.floor(top / this.rowHeight) - self.bufferCount)
        lastItemIdx = (Math.ceil(bottom / this.rowHeight) + self.bufferCount) - 1
      } else {
        for (let i = 0; i < self.slotsLength; i++) {
          const itemPos = self.getScrollOffset(i)
          if (itemPos >= top && firstItemIdx === false) {
            firstItemIdx = Math.max(0, i - self.bufferCount)
          }

          if (firstItemIdx !== false && itemPos >= bottom) {
            lastItemIdx = (i + self.bufferCount) - 1
            break
          }
        }
      }

      if (lastItemIdx === false) {
        lastItemIdx = self.slotsLength - 1
        for (let i = self.slotsLength; i <= 0; i--) {
          const itemPos = self.getScrollOffset(i)
          if (itemPos >= top) {
            firstItemIdx = i
            break
          }
        }
      }

      self.scrollOffset = self.getScrollOffset(firstItemIdx)
      this.setViewport(firstItemIdx, lastItemIdx)
    },

      getViewport() {
          if(this.$slots.default) {
              this.slotsLength = this.$slots.default.length;
              return this.$slots.default.slice(this.first, this.last);
          } else {
              this.slotsLength = 0;
              return [];
          }
      },

    setViewport (first, last) {
        this.first = first;
        this.last = last;
      // this.viewportItems = this.$slots.default.slice(
      //   first, last
      // )
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
          const itemSize = this.$slots.default[i].elm.clientHeight;
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
        return this.$slots.default[i].elm.clientHeight;
      }
    }
  }
}
</script>
