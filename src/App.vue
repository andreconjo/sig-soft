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
      :height="800"/>
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
          },
          {
            id: 4,
            x: -580,
            y: -80,          
            type: 'softgoal',
            label: 'Segurança',
          },
          {
            id: 6,
            x: -390,
            y: 80,
            type: 'Rule',
            label: 'PIN',
          }
        ],
        links: [
          {
            id: 3,
            from: 2, // node id the link start
            to: 4,  // node id the link end
          },
          {
            id: 6,
            from: 6, // node id the link start
            to: 4,  // node id th
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
      console.log('addNode')
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
      })
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
