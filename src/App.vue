<template>
  <div id="app">
    <h1> SIG Soft</h1>

  <aside class="aside aside-left">
    <toolbox @addNodeToolbox="addNode"/>
  </aside>


  <aside  class="aside aside-right">
    <h1>Catálogo</h1>
  </aside>
    
    <simple-flowchart :scene.sync="scene" 
      @nodeClick="nodeClick"
      @nodeDelete="nodeDelete"
      @linkBreak="linkBreak"
      @linkAdded="linkAdded"
      @canvasClick="canvasClick"
      :height="800"
      :arrowType="arrowType"/>

  </div>
</template>

<script>
import SimpleFlowchart from './components/SimpleFlowchart.vue'
import toolbox from './components/aside/toolbox.vue'

export default {
  name: 'app',
  components: {
    SimpleFlowchart,
    toolbox
  },
  data() {
    return {
      arrowType: '',
      scene: {
        centerX: 1024,
        centerY: 140,
        scale: 1,
        nodes: [
          {
            id: 2,
            x: -770,
            y: 80,
            type: 'action',
            label: 'Autenticação',
            priority: false,
            topic: '',
            satisfact: ''
          },
          {
            id: 4,
            x: -580,
            y: -80,          
            type: 'softgoal',
            label: 'Segurança',
            priority: false,
            topic: '',
            satisfact: ''
          },
          {
            id: 6,
            x: -390,
            y: 80,
            type: 'action',
            label: 'PIN',
            priority: false,
            topic: '',
            satisfact: ''
          }
        ],
        links: [
          {
            id: 3,
            from: 2, // node id the link start
            to: 4, // node id the link end
          },
          {
            id: 6,
            from: 6, // node id the link start
            to: 4,  // node id the link end
          }
        ]
      },
      newNodeType: 0,
      newNodeLabel: '',
      nodeCategory:[
        'Softgoal',
      ],
    }
  },
  methods: {
    canvasClick(e) {
      //console.log('canvas Click, event:', e)
    },
    addNode(type) {
      console.log(type)
      if(type === 'arrow-or' || type === 'arrow-and')
        this.arrowType = type;
      else {
        let maxID = Math.max(0, ...this.scene.nodes.map((link) => {
          return link.id
        }))
        this.scene.nodes.push({
          id: maxID + 1,
          x: -1000,
          y: -100,
          // type: this.nodeCategory[this.newNodeType],
          type: type,
          label: this.newNodeLabel ? this.newNodeLabel: `${type}`,
          priority: false,
          topic: '',
          satisfact: ''
        })
      }
    },
    nodeClick(id) {
      console.log('node click', id);
    },
    nodeDelete(id) {
      console.log('node delete', id);
    },
    linkBreak(id) {
      console.log('link break', id);
    },
    linkAdded(link) {
      console.log('new link added:', link);
    },
    findNodeById(id) {
      return this.scene.nodes.filter(node => node.id === id)[0];
    },
    findLinkByFromId(id) {
      return this.scene.links.filter(link => link.from === id);
    },
    findLinkByToId(id) {
      return this.scene.links.filter(link => link.to === id);
    },
    findNodesChildren(links) {
       
      return links.map(link => {
        return this.findNodeById(link.from);
      })
    },
    changeSatisfact(position){ 
      this.scene.nodes.map(node => {
          let links = this.findLinkByToId(node.id);
          let children = this.findNodesChildren(this.findLinkByToId(node.id));

          console.log('Nó de referencia:', node.id, '| Links:', links, "| filhos: ", children)


      })
    }
  },
  computed: {
    selected() {
      return this.scene.nodes.map(node => node.satisfact)
    }
  },
  watch: {
    selected() {
      this.changeSatisfact()
    }
  }
}
</script>

<style lang="scss">
.aside {
    width: 250px; 
    height: 100%;
}
.aside-left {
    float:left; 
    border-right: 1px solid black; 
}

.aside-right {
  float:right; 
  border-left: 1px solid black; 
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  overflow: hidden;
  height: 800px;
  .tool-wrapper {
    position: relative;
  }
}
</style>
