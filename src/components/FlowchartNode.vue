<template>
  <div class="flowchart-node" :style="nodeStyle" 
    @mousedown="handleMousedown"
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
    @dblclick="handleDblClick"
    @keypress.enter="handleEnter"
    v-bind:class="`${handleTypeImage(type)}`">
    <div class="node-port node-output"
       @mousedown="inputMouseDown"
       @mouseup="inputMouseUp">
    </div>
    <div class="node-main">
      <div v-text="type" class="node-type"></div>
      <div v-show="!isEditing" class="node-label">{{label}}</div>
      <input ref="editLabel" v-show="isEditing" type="text" v-model="localLabel"/>
    </div>
    <div class="node-port node-input" 
      @mousedown="outputMouseDown">
    </div>
    <div v-show="show.delete" class="node-delete">&times;</div>
  </div>
</template>

<script>
export default {
  name: 'FlowchartNode',
  props: {
    id: {
      type: Number,
      default: 1000,
      validator(val) {
        return typeof val === 'number'
      }
    },
    x: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },    
    y: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },
    type: {
      type: String,
      default: 'Default'
    },
    label: {
      type: String,
      default: 'input name'
    },
    options: {
      type: Object,
      default() {
        return {
          centerX: 1024,
          scale: 1,
          centerY: 140,
        }
      }
    }
  },
  data() {
    return {
      show: {
        delete: false,
      },
      isEditing: false,
      localLabel: ''
    }
  },
  
  mounted() {
    
  },
  computed: {
    nodeStyle() {
      return {
        top: this.options.centerY + this.y * this.options.scale + 'px', // remove: this.options.offsetTop + 
        left: this.options.centerX + this.x * this.options.scale + 'px', // remove: this.options.offsetLeft + 
        transform: `scale(${this.options.scale})`,
      }
    }
  },
  methods: {

    handleEnter() {
      this.isEditing = false;
      this.saveLabelChange()
    },
    handleMousedown(e) {
      const target = e.target || e.srcElement;
      // console.log(target);
      if (target.className.indexOf('node-input') < 0 && target.className.indexOf('node-output') < 0) {
        this.$emit('nodeSelected', e);
      }
      e.preventDefault();
    },
    handleMouseOver() {
      this.show.delete = true;
    },
    handleMouseLeave() {
      this.show.delete = false;
    },
    outputMouseDown(e) {
      this.$emit('linkingStart')
      e.preventDefault();
    },
    inputMouseDown(e) {
      e.preventDefault();
    },
    inputMouseUp(e) {
      this.$emit('linkingStop')
      e.preventDefault();
    },
    handleDblClick(e) {
      this.isEditing = true
      this.localLabel = this.label
      this.$refs.editLabel.focus();
      e.preventDefault()
    },
    handleTypeImage(type) {
      if (type == 'softgoal') 
        return 'flowchart-softgoal'
      
      if (type == 'claim') 
        return 'flowchart-claim'
      

      return 'flowchart-default'
    },
    saveLabelChange() {
      this.$emit('changeLabel', this.id, this.localLabel)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$themeColor: rgb(116, 77, 60);
$portSize: 12;

.flowchart-default {
  background-image: url('../assets/images/action.png');
}

.flowchart-softgoal {
  background-image: url('../assets/images/softgoal.png');
}

.flowchart-claim {
  background-image: url('../assets/images/claim.png');
}

.flowchart-node {
  
  margin: 0;
  width: 120px;
  height: 80px;
  position: absolute;
  box-sizing: border-box;
  border: none;
  background-repeat: no-repeat;
  background-size: contain;
  background-position: center;
  z-index: 1;
  opacity: .9;
  cursor: move;
  display: flex;
    justify-content: center;
    align-items: center;
  transform-origin: top left;
  
  .node-main {
    
    .node-type {
      display: none;
      background: $themeColor;
      color: white;
      font-size: 13px;
      padding: 6px;
    }
    .node-label {
      font-size: 13px;
    }
  }
  .node-port {
    position: absolute;
    width: #{$portSize}px;
    height: #{$portSize}px;
    left: 50%;
    transform: translate(-50%);
    border: 1px solid #ccc;
    border-radius: 100px;
    background: white;
    &:hover {
      background: $themeColor;
      border: 1px solid $themeColor;
    }
  }
  .node-input {
    top: #{-2+$portSize/-2}px;
  }
  .node-output {
    bottom: #{-2+$portSize/-2}px;
  }
  .node-delete {
    position: absolute;
    right: -6px;
    top: -6px;
    font-size: 12px;
    width: 12px;
    height: 12px;
    color: $themeColor;
    cursor: pointer;
    background: white;
    border: 1px solid $themeColor;
    border-radius: 100px;
    text-align: center;
    &:hover{
      background: $themeColor;
      color: white;
    }
  }
}
.selected {
  box-shadow: 0 0 0 2px $themeColor;
}
</style>
