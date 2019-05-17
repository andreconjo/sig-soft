<template>
  <g @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave">
    <path :d="dAttr" :style="pathStyle"></path>
    <a v-if="show.delete" @click="deleteLink">
      <text 
        text-anchor="middle" 
        :transform="arrowTransform"
        font-size="22">&times;</text>
    </a>
    <path v-else d="M -1 -1 L 0 1 L 1 -1 z"
      :style="arrowStyle"
      :transform="arrowTransform"></path>
  </g>
</template>

<script>
export default {
  name: 'FlowchartLink',
  props: {
    type: '',
    // start point position [x, y]
    start: {
      type: Array,
      default() {
        return [0, 0]
      }
    },
    // end point position [x, y]
    end: {
      type: Array,
      default() {
        return [0, 0]
      }
    },
    id: Number,
  },
  data() {
    return {
      myArrowType: '',
      show: {
        delete: false,
      }
    }
  },
  watch: {
    type: function() {
      console.log("Watch: ", this.myArrowType, this.id, this.start, this.end)
    }
  },
  methods: {
    handleMouseOver() {
      if (this.id) {
        this.show.delete = true;
      }
    },
    handleMouseLeave() {
      this.show.delete = false;
    },
    caculateCenterPoint() {
      // caculate arrow position: the center point between start and end
      const dx = (this.end[0] - this.start[0]) / 2;
      const dy = (this.end[1] - this.start[1]) / 2;
      return [this.start[0] + dx, this.start[1] + dy];
    },
    caculateRotation() {
      // caculate arrow rotation
      const angle = -Math.atan2(this.end[0] - this.start[0], this.end[1] - this.start[1]);
      const degree = angle * 180 / Math.PI;
      return degree < 0 ? degree + 360 : degree;
    },
    deleteLink() {
      this.$emit('deleteLink')
    }
  },
  mounted() {
    this.myArrowType = this.type
  },
  computed: {
    pathStyle() {
      let color = 'rgb(0, 0, 0)';

      if(this.myArrowType === 'arrow-or')
        color = 'rgb(255, 0, 0)'
      else if (this.myArrowType === 'arrow-and')
        color = 'rgb(0, 255, 0)'
      
      return {
        stroke: color,
        strokeWidth: 2.73205,
        fill: 'none',
      }
    },
    arrowStyle() {
      return {
        stroke: 'rgb(0, 0, 0)',
        strokeWidth: 5.73205,
        fill: 'none',
      }
    },
    arrowTransform() {
      const [arrowX, arrowY] = this.caculateCenterPoint();
      const degree = this.caculateRotation()
      return `translate(${arrowX}, ${arrowY}) rotate(${degree})`;
    },
    dAttr() {
      let cx = this.end[0], cy = this.end[1], ex = this.start[0], ey = this.start[1];
      let x1 = cx, y1 = cy + 50, x2 = ex, y2 = ey - 50;
      return `M ${cx}, ${cy} C ${x1}, ${y1}, ${x2}, ${y2}, ${ex}, ${ey}`;
    }
  }
}
</script>

<style scoped lang="scss">
g {
  cursor: pointer;
}
</style>