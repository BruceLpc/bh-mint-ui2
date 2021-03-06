<template>
  <div class="mint-picker-column" :class="className">
    <div class="mint-picker-column__frame mint-hairline--top-bottom mt-bColor-after-grey-lv6" :style="frameStyle" />
    <ul
      :style="wrapperStyle"
      @touchstart="onTouchStart"
      @touchmove.prevent="onTouchMove"
      @touchend="onTouchEnd"
      @touchcancel="onTouchEnd"
    >
      <li
        v-for="(option, index) in options"
        class="mt-picker-column-item"
        :class="[
          {'mint-picker-column--disabled': isDisabled(option)},
          {'mint-picker-column--selected': index === currentIndex},
          index === currentIndex?'mt-color-theme':'mt-color-grey'
        ]"
        @click="setIndex(index)"
        :style="frameStyle">
          <div>
            <span v-text="getOptionText(option)" :style="frameStyle"></span>
          </div>
        </li>
    </ul>
  </div>
</template>

<style>
  .mt-picker-column-item div{
    display: inline-block;
  }
  .mt-picker-column-item div span{
    display: table-cell;
    vertical-align: middle;
  }
</style>
<script>
const DEFAULT_DURATION = 200;
const range = (num, arr) => Math.min(Math.max(num, arr[0]), arr[1]);
export default {
  name: 'mt-picker-column',
  props: {
    valueKey: String,
    columnIndex:Number,
    className: String,
    options: {
      type: Array,
      default: () => []
    },
    itemHeight: {
      type: Number,
      default: 44
    },
    visibileColumnCount: {
      type: Number,
      default: 5
    },
    defaultIndex: {
      type: Number,
      default: 0
    },
    isInterrelated:{
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      startY: 0,
      offset: 0,
      duration: 0,
      startOffset: 0,
      currentIndex: this.defaultIndex
    };
  },
  created() {
    this.$parent && this.$parent.children.push(this);
  },
  mounted() {
    this.setIndex(this.currentIndex);
  },
  destroyed() {
    this.$parent && this.$parent.children.splice(this.$parent.children.indexOf(this), 1);
  },
  watch: {
    defaultIndex() {
      this.setIndex(this.defaultIndex);
    },
    options(next, prev) {
      if (JSON.stringify(next) !== JSON.stringify(prev)) {
        this.setIndex(this.defaultIndex);
      }
    },
    currentIndex(index) {
      this.$emit('change', this.columnIndex ,index);
    }
  },
  computed: {
    count() {
      return this.options.length;
    },
    wrapperStyle() {
      const { itemHeight, visibileColumnCount } = this;
      //lineHeight: itemHeight + 'px',
      return {
        transition: `${this.duration}ms`,
        transform: `translate3d(0, ${this.offset}px, 0)`,
        height: itemHeight * visibileColumnCount + 'px',
        paddingTop: itemHeight * (visibileColumnCount - 1) / 2 + 'px'
      };
    },
    frameStyle() {
      return {
        height: this.itemHeight + 'px'
      }
    },
    currentValue() {
      return this.options[this.currentIndex];
    }
  },
  methods: {
    onTouchStart(event) {
      this.startY = event.touches[0].clientY;
      this.startOffset = this.offset;
      this.duration = 0;
    },
    onTouchMove(event) {
      const deltaY = event.touches[0].clientY - this.startY;
      this.offset = range(this.startOffset + deltaY, [
        -(this.count * this.itemHeight),
        this.itemHeight
      ]);
    },
    onTouchEnd() {
      if (this.offset !== this.startOffset) {
        this.duration = DEFAULT_DURATION;
        const index = range(Math.round(-this.offset / this.itemHeight), [
          0,
          this.count - 1
        ]);
        this.setIndex(index);
      }
    },
    adjustIndex(index) {
      index = range(index, [0, this.count]);
      for (let i = index; i < this.count; i++) {
        if (!this.isDisabled(this.options[i])) return i;
      }
      for (let i = index - 1; i >= 0; i--) {
        if (!this.isDisabled(this.options[i])) return i;
      }
    },
    isDisabled(option) {
      return typeof option === 'object' && option.disabled;
    },
    getOptionText(option) {
      return typeof option === 'object' && this.valueKey in option ? option[this.valueKey] : option;
    },
    setIndex(index) {
      index = this.adjustIndex(index);
      this.offset = -index * this.itemHeight;
      this.currentIndex = index;
    },
    setValue(value) {
      const { options, valueKey } = this;
      for (let i = 0; i < options.length; i++) {
        if (this.getOptionText(options[i]) === value) {
          this.setIndex(i);
          return;
        }
      }
    }
  }
};
</script>