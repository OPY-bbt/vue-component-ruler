<template>
  <div class="slide">
    <div class="sticks" ref="wrapper">
      <span class="singleStick" v-for="(s, idx) in sticks" :key="s.sticks">
        <i class="tips" v-if="(idx + 10) % 10 === 0">{{ `${idx * 0.1}.0` }}</i>
      </span>
    </div>
    <span class="singleStick active"></span>
    <i class="baseline"></i>
  </div>
</template>

<script>
import draggable from './draggable';
import translateUtil from './translate';

export default {
  props: {
    value: {
      type: Number,
      default: 0
    },
    range: {
      default () {
        return [0, 300];
      }
    },
    itemWidth: {
      type: Number,
      default: 24
    },
    visibleItemCount: {
      type: Number,
      default: 30
    }
  },
  methods: {
    initEvents() {
      const el = this.$refs.wrapper;

      let dragState = {};
      let velocityTranslate;
      let prevTranslate;
      // let pickerItems;

      draggable(el, {
        start: (event) => {
          cancelAnimationFrame(this.animationFrameId);
          this.animationFrameId = null;
          dragState = {
            range: this.dragRange,
            start: new Date(),
            startLeft: event.pageX,
            startTop: event.pageY,
            startTranslateLeft: translateUtil.getElementTranslate(el).left
          };
          // pickerItems = el.querySelectorAll('.singleStick');
        },

        drag: (event) => {
          this.dragging = true;
          dragState.left = event.pageX;
          dragState.top = event.pageY;

          const deltaX = dragState.left - dragState.startLeft;
          const translate = dragState.startTranslateLeft + deltaX;

          translateUtil.translateElement(el, translate, null);

          velocityTranslate = translate - prevTranslate || translate;
          prevTranslate = translate;
        },

        end: (event) => {
          this.dragging = false;
          const momentumRatio = 7;
          let currentTranslate = translateUtil.getElementTranslate(el).left;
          const duration = new Date() - dragState.start;
          let distance = Math.abs(dragState.startTranslateLeft - currentTranslate);
          const itemWidth = 24;
          // const visibleItemCount = this.visibleItemCount;

          let rect, offset;
          if (distance < 6) {
            console.log('distance<6', rect, offset);
          }

          let momentumTranslate;
          if (duration < 300) {
            momentumTranslate = currentTranslate + velocityTranslate * momentumRatio
          }

          const dragRange = dragState.range;

          this.$nextTick(() => {
            var translate;
            if (momentumTranslate) {
              translate = Math.round(momentumTranslate / itemWidth) * itemWidth;
            } else {
              translate = Math.round(currentTranslate / itemWidth) * itemWidth;
            }

            translate = Math.max(Math.min(translate, dragRange[1]), dragRange[0]);

            translateUtil.translateElement(el, translate, null);

            this.currentValue = this.translate2Value(translate);
          });

          dragState = {};
        }
      });
    },
    translate2Value(translate) {
      const itemWidth = this.itemWidth;
      translate = Math.round(translate / itemWidth) * itemWidth;
      const index = -(translate - Math.floor(this.visibleItemCount / 2) * itemWidth) / itemWidth;
      return index;
    },
    value2Translate(index) {
      const itemWidth = this.itemWidth;
      const translate = -index * itemWidth + Math.floor(this.visibleItemCount / 2) * itemWidth;

      return translate;
    },
    doOnValueChange () {
      var value = this.currentValue;
      var wrapper = this.$refs.wrapper;

      translateUtil.translateElement(wrapper, this.value2Translate(value), null);
    }
  },
  computed: {
    dragRange () {
      var values = this.sticks;
      var visibleItemCount = this.visibleItemCount;
      var itemWidth = this.itemWidth;
      return [ -itemWidth * (values.length - Math.ceil(visibleItemCount / 2)), itemWidth * Math.floor(visibleItemCount / 2) ];
    }
  },
  data () {
    const range = this.range;
    const len = range[1] - range[0];
    return {
      sticks: (new Array(len)).fill(''),
      currentValue: this.value
    }
  },
  mounted () {
    this.initEvents();
    this.doOnValueChange();
  },
  watch: {
    currentValue(val) {
      this.$emit('onchange', val);
    }
  }
}
</script>

<style scoped>
  .slide{
    width: 750px;
    overflow: hidden;
    padding-bottom: 30px;
    position: relative;
    user-select: none;
  }
  .sticks{
    width: max-content;
    position: relative;
    height: 124px;
    left: 0px;
    bottom: -38px;
    white-space: nowrap;
  }
  .singleStick{
    display: inline-block;
    position: relative;
    width: 24px;
    height: 56px;
    /* background-color: #c2c2c2; */
    box-sizing: border-box;
    border-right: 2px solid #c2c2c2;
  }
  .singleStick.active {
    position: absolute;
    left: 50%;
    transform: translateX(-15px);
    border-color: #5199f7;
    height: 124px;
    bottom: 47px;
  }
  .singleStick:nth-of-type(5n-4){
    height: 88px;
  }
  .baseline{
    position: relative;
    display: inline-block;
    width: 100%;
    height: 2px;
    background-color: #c2c2c2;
    left: 0px;
    top: -13px;
  }
  .tips{
    position: absolute;
    bottom: -30px;
    left: 6px;
    color: #666666;
    font-size: 24px;
    font-style: normal;
  }
</style>
