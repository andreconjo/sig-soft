<template>
  <div class="flowchart-container" 
    @mousemove="handleMove" 
    @mouseup="handleUp"
    @mousedown="handleDown"
    oncontextmenu="return false">
    
    <svg width="100%" :height="`${height}px`">
      <flowchart-link v-bind.sync="link" 
        v-for="(link, index) in lines" 
        :key="`link${index}`"
        :type="link.type"
        @deleteLink="linkDelete(link.id)">
      </flowchart-link>
    </svg>

    <flowchart-node v-bind.sync="node" 
      v-for="(node, index) in scene.nodes" 
      :key="`node${index}`"
      :options="nodeOptions"
      @linkingStart="linkingStart(node.id)"
      @linkingStop="linkingStop(node.id)"
      @nodeSelected="nodeSelected(node.id, $event)"
      @changeLabel="changeNodeLabel"
      @setProperty="setNodeProperty"
      @pushSelectedNode="pushNode"
      @popSelectedNode="popNode"
      :isModal="isModal"
      >
    </flowchart-node>
  </div>
</template>

<script>
import FlowchartLink from './FlowchartLink.vue';
import FlowchartNode from './FlowchartNode.vue';
import { getMousePosition } from '../assets/utilty/position';

export default {
  name: 'VueFlowchart',
  props: {
    isModal: {
      type: Boolean,
      default: false
    },
    arrowType: {
      type: String,
      default: ''
    },
    scene: {
      type: Object,
      default() {
        return {
          centerX: 1024,
          scale: 1,
          centerY: 140,
          nodes: [],
          links: [],
        }
      }
    },
    height: {
      type: Number,
      default: 400,
    },
  },
  data() {
    return {
      selectedNodes: [],
      selectedLinks: [],
      action: {
        linking: false,
        dragging: false,
        scrolling: false,
        selected: 0,
      },
      mouse: {
        x: 0,
        y: 0,
        lastX: 0,
        lastY: 0,
      },
      draggingLink: null,
      rootDivOffset: {
        top: 0,
        left: 0
      },
    };
  },
  components: {
    FlowchartLink,
    FlowchartNode
  },
  computed: {
    nodeOptions() {
      return {
        centerY: this.scene.centerY,
        centerX: this.scene.centerX,
        scale: this.scene.scale,
        offsetTop: this.rootDivOffset.top,
        offsetLeft: this.rootDivOffset.left,
        selected: this.action.selected,
      }
    },
    lines() {
      const lines = this.scene.links.map((link) => {
        const fromNode = this.findNodeWithID(link.from)
        const toNode = this.findNodeWithID(link.to)
        let x, y, cy, cx, ex, ey;
        x = this.scene.centerX + fromNode.x;
        y = this.scene.centerY + fromNode.y;
        [cx, cy] = this.getPortPosition('top', x, y);
        x = this.scene.centerX + toNode.x;
        y = this.scene.centerY + toNode.y;
        [ex, ey] = this.getPortPosition('bottom', x, y);
        return { 
          start: [cx, cy], 
          end: [ex, ey],
          id: link.id,
          type: link.type
        };
      })
      if (this.draggingLink) {
        let x, y, cy, cx;
        const fromNode = this.findNodeWithID(this.draggingLink.from)
        x = this.scene.centerX + fromNode.x;
        y = this.scene.centerY + fromNode.y;
        [cx, cy] = this.getPortPosition('top', x, y);
        // push temp dragging link, mouse cursor postion = link end postion 
        lines.push({ 
          start: [cx, cy], 
          end: [this.draggingLink.mx, this.draggingLink.my],
        })
      }
      return lines;
    }
  },
  mounted() {
    this.rootDivOffset.top = this.$el ? this.$el.offsetTop : 0;
    this.rootDivOffset.left = this.$el ? this.$el.offsetLeft : 0;

    console.log('Minha cena: ', this.scene);
  },
  methods: {
    getParentes() {
      this.selectedLinks = []
      this.scene.links.map(link => {
        if(this.selectedNodes.filter(n => n.id == link.from).length > 0 && this.selectedNodes.filter(n => n.id == link.to).length > 0){
          this.selectedLinks.push(link);
        }
      })

    },
    pushNode(node) {
      this.selectedNodes.push(node);
      this.getParentes();
      this.$emit('setSelectedNodes', this.selectedNodes)
      this.$emit('setSelectedLinks', this.selectedLinks)
    },
    popNode(node) {
      this.selectedNodes = this.selectedNodes.filter(nd => nd.id !== node.id);
      this.getParentes();
      this.$emit('setSelectedNodes', this.selectedNodes)
      this.$emit('setSelectedLinks', this.selectedLinks)
    },
    changeNodeLabel(id, label) {
      this.findNodeWithID(id).label = label;
    },
    findNodeWithID(id) {
      return this.scene.nodes.find((item) => {
          return id === item.id
      })
    },
    getPortPosition(type, x, y) {
      if (type === 'top') {
        return [x + 60, y];
      }
      else if (type === 'bottom') {
        return [x + 60, y + 80];
      }
    },
    linkingStart(index) {
      if(this.isModal)
        return;
      this.action.linking = true;
      this.draggingLink = {
        from: index,
        mx: 0,
        my: 0,
      };
    },
    linkingStop(index) {
      // add new Link
      if (this.draggingLink && this.draggingLink.from !== index) {
        // check link existence
        const existed = this.scene.links.find((link) => {
          return link.from === this.draggingLink.from && link.to === index;
        })
        if (!existed) {
          let maxID = Math.max(0, ...this.scene.links.map((link) => {
            return link.id
          }))
          const newLink = {
            id: maxID + 1,
            from: this.draggingLink.from,
            to: index,
            type: this.arrowType
          };
          this.scene.links.push(newLink)
          this.$emit('linkAdded', newLink)
        }
      }
      this.draggingLink = null
    },
    linkDelete(id) {
      if(this.isModal)
        return;

      const deletedLink = this.scene.links.find((item) => {
          return item.id === id;
      });
      if (deletedLink) {
        this.scene.links = this.scene.links.filter((item) => {
            return item.id !== id;
        });
        this.$emit('linkBreak', deletedLink);
      }
    },
    nodeSelected(id, e) {
      if(this.isModal)
        return;
      this.action.dragging = id;
      this.action.selected = id;
      this.$emit('nodeClick', id);
      this.mouse.lastX = e.pageX || e.clientX + document.documentElement.scrollLeft
      this.mouse.lastY = e.pageY || e.clientY + document.documentElement.scrollTop
    },
    handleMove(e) {
      if (this.action.linking) {
        [this.mouse.x, this.mouse.y] = getMousePosition(this.$el, e);
        [this.draggingLink.mx, this.draggingLink.my] = [this.mouse.x, this.mouse.y];
      }
      if (this.action.dragging) {
        this.mouse.x = e.pageX || e.clientX + document.documentElement.scrollLeft
        this.mouse.y = e.pageY || e.clientY + document.documentElement.scrollTop
        let diffX = this.mouse.x - this.mouse.lastX;
        let diffY = this.mouse.y - this.mouse.lastY;

        this.mouse.lastX = this.mouse.x;
        this.mouse.lastY = this.mouse.y;
        this.moveSelectedNode(diffX, diffY);
      }
      if (this.action.scrolling) {
        [this.mouse.x, this.mouse.y] = getMousePosition(this.$el, e);
        let diffX = this.mouse.x - this.mouse.lastX;
        let diffY = this.mouse.y - this.mouse.lastY;

        this.mouse.lastX = this.mouse.x;
        this.mouse.lastY = this.mouse.y;

        this.scene.centerX += diffX;
        this.scene.centerY += diffY;

        // this.hasDragged = true
      }
    },
    handleUp(e) {
      const target = e.target || e.srcElement;
      if (this.$el.contains(target)) {
        if (typeof target.className !== 'string' || target.className.indexOf('node-input') < 0) {
          this.draggingLink = null;
        }
        if (typeof target.className === 'string' && target.className.indexOf('node-delete') > -1) {
          this.nodeDelete(this.action.dragging);
        }
      }
      this.action.linking = false;
      this.action.dragging = null;
      this.action.scrolling = false;
    },
    handleDown(e) {
      
      const target = e.target || e.srcElement;

      // console.log('for scroll', target, e.keyCode, e.which)
      if ((target === this.$el || target.matches('svg, svg *')) && e.which === 1) {
        this.action.scrolling = true;
        [this.mouse.lastX, this.mouse.lastY] = getMousePosition(this.$el, e);
        this.action.selected = null; // deselectAll
      }
      this.$emit('canvasClick', e);
      
    },
    moveSelectedNode(dx, dy) {
      if(this.isModal)
        return;
      let index = this.scene.nodes.findIndex((item) => {
        return item.id === this.action.dragging
      })
      let left = this.scene.nodes[index].x + dx / this.scene.scale;
      let top = this.scene.nodes[index].y + dy / this.scene.scale;
      this.$set(this.scene.nodes, index, Object.assign(this.scene.nodes[index], {
        x: left,
        y: top,
      }));
    },
    nodeDelete(id) {
      if(this.isModal)
        return;
      this.scene.nodes = this.scene.nodes.filter((node) => {
        return node.id !== id;
      })
      this.scene.links = this.scene.links.filter((link) => {
        return link.from !== id && link.to !== id
      })
      this.$emit('nodeDelete', id)
    },
    setNodeProperty(property) {
      if(this.isModal)
        return;
      let myNode = this.findNodeWithID(property.id)
      myNode[property.property] = property.value
      this.$emit("callSuper")
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.flowchart-container {
  margin: 0;
  background: #ecf1f2;
  position: relative;
  overflow: hidden;
  svg {
    cursor: grab;
  }
}
</style>
