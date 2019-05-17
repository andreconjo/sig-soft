<template>
  <div class="flowchart-node" :style="nodeStyle" 
    @mousedown="handleMousedown"
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
    @dblclick="handleDblClick"
    @keypress.enter="handleEnter"
    v-bind:class="`${handleTypeImage(type)}`"
    >
    <div class="node-port node-output"
       @mousedown="inputMouseDown"
       @mouseup="inputMouseUp">
    </div>
    
    <div v-show="satisfact" v-bind:class="`${handleSafisfact()}`"></div>
    <div v-show="!showContext" class="node-main">
      <div v-text="type" class="node-type"></div>
      <div v-show="!isEditing" class="node-label">{{label}}</div>
      <div v-show="topic" class="node-label">[{{topic}}]</div>
      <input ref="editLabel" v-show="isEditing" type="text" v-model="localLabel"/>
    </div>
    <div class="node-port node-input" 
      @mousedown="outputMouseDown">
    </div>
    <div v-show="show.delete" class="node-delete">&times;</div>
    
    <div style="position: relative">
      <contextbox v-if="showContext" :satifactProp="satisfact" :topicProp="topic" :priorityProp="priority" :mouseEvent="mouseEvent" @setAttributes="setNodeAttributes"/>
    </div>
  </div>
</template>

<script>

import contextbox from './click/contextbox.vue'

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
    priority: {
      type: Boolean,
      default: false
    },
    topic: {
      type: String,
      default: ''
      
    },
    satisfact: {
      type: String,
      default: ''
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
  components: {
    contextbox
  }, 
  data() {
    return {
      mouseEvent: null,
      showContext: false,
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
           if(this.showContext)
        return
      this.isEditing = false;
      this.saveLabelChange()
    },
    handleMousedown(e) {
      const target = e.target || e.srcElement;
      // console.log(target);
      if(e.button === 2) {
        this.showContext = !this.showContext;
        e.preventDefault();
        
        this.mouseEvent = {
          pageX:  e.pageX + 200,
          pageY: e.pageY  + 300
        }
        
        return;
      }

           if(this.showContext)
        return
      
      if (target.className.indexOf('node-input') < 0 && target.className.indexOf('node-output') < 0) {
        this.$emit('nodeSelected', e);
      }
      e.preventDefault();
    },
    handleMouseOver() {
           if(this.showContext)
        return
      this.show.delete = true;
    },
    handleMouseLeave() {
           if(this.showContext)
        return
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
      if(this.showContext)
        return
      this.isEditing = true
      this.localLabel = this.label
      this.$refs.editLabel.focus();
      e.preventDefault()
    },
    handleSafisfact() {
      switch(this.satisfact) {
        case 'Denied':
          return 'icon denied'
        case 'Weakly Denied':
          return 'icon weakly-denied'
        case 'Weakly Satisficed':
          return 'icon weakly-satisficed'
        case 'Satisficed':
          return 'icon satisficed'
        case 'Conflict':
          return 'icon conflict'
      }
        
        
    },
    handleTypeImage(type) {
      if (type == 'softgoal') 
        return 'flowchart-softgoal'
      
      if (type == 'claim') 
        return 'flowchart-claim'

      if (type == 'action') 
        return 'flowchart-default'
      

      return ''
    },
    saveLabelChange() {
      this.$emit('changeLabel', this.id, this.localLabel)
    },
    setNodeAttributes(property) {
      property["id"] = this.id
      this.$emit('setProperty', property)
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

.icon {
    background-size: 14px 14px;
    width: 16px;
    height: 16px;
    background-repeat: no-repeat;
    position: absolute;
    margin-left: 60px;
    margin-top: -10px;

    &.denied{
      background-image: url('../assets/images/denied.png');
    }

    &.weakly-denied{
      background-image: url('../assets/images/weakly-denied.png');
    }

    &.weakly-satisficed{
      background-image: url('../assets/images/weakly-satisficed.png');
    }

    &.satisficed{
      background-image: url('../assets/images/satisficed.png');
    }

    &.conflict{
      background-image: url('../assets/images/conflict.png');
    }
}
</style>
