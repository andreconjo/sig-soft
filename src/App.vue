<template>
  <div id="app">
    <div v-if="openModal">
      <modal :scene="selectedScenes"/>
    </div>
    <h1>SIG Soft</h1>

    <aside class="aside aside-left">
      <toolbox @addNodeToolbox="addNode"/>
    </aside>

    <aside class="aside aside-right">
      <catalog @openModal="handleModal"/>
    </aside>

    <simple-flowchart
      :scene.sync="scene"
      @nodeClick="nodeClick"
      @nodeDelete="nodeDelete"
      @linkBreak="linkBreak"
      @linkAdded="linkAdded"
      @canvasClick="canvasClick"
      @callSuper="changeSatisfact"
      :height="800"
      :arrowType="arrowType"
    />
  </div>
</template>

<script>
import SimpleFlowchart from './components/SimpleFlowchart.vue'
import toolbox from './components/aside/toolbox.vue'
import catalog from './components/aside/catalog.vue'
import modal from './components/utils/modal.vue'

export default {
  name: 'app',
  components: {
    SimpleFlowchart,
    toolbox,
    catalog,
    modal
  },
  data() {
    return {
      arrowType: '',
      selectedScenes: {},
      openModal: false,
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
      if(type === 'arrow-or' || type === 'arrow-and' || type === 'arrow-make'
      || type === 'arrow-help' || type === 'arrow-hurt' || type === 'arrow-break')
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
      //console.log('node click', id);
    },
    handleModal(scene) {
      this.selectedScenes = scene;
      this.openModal = true;
    },
    nodeDelete(id) {
      //console.log('node delete', id);
    },
    linkBreak(id) {
      //console.log('link break', id);
    },
    linkAdded(link) {
      //console.log('new link added:', link);
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
    getChildWithArrowAnd(fatherId) {
      return this.findLinkByToId(fatherId).filter(link => link.type === 'arrow-and');
    },
    getChildWithArrowOr(fatherId) {
      return this.findLinkByToId(fatherId).filter(link => link.type === 'arrow-or');
    },
    allIsSatisfact(fatherId, type){
      let childs = [];

      if(type === 'and')
        this.getChildWithArrowAnd(fatherId).map(link => {
          childs.push(this.findNodeById(link.from))

        });

      if(type === 'or')
        this.getChildWithArrowOr(fatherId).map(link => {
          childs.push(this.findNodeById(link.from))

        });

      let checked = childs.filter(node => node.satisfact === 'Satisficed');
      return childs.length === checked.length
    },
    haveJustOneSatisfact(fatherId) {
      let childs = [];

      this.getChildWithArrowOr(fatherId).map(link => {
        childs.push(this.findNodeById(link.from))
      });

      let checked = childs.filter(node => node.satisfact === 'Satisficed');
      return checked.length > 0
    },
    allHaveStatus(fatherId, type) {
       let childs = [];

      if(type === 'and')
        this.getChildWithArrowAnd(fatherId).map(link => {
          childs.push(this.findNodeById(link.from))

        });

      if(type === 'or')
        this.getChildWithArrowOr(fatherId).map(link => {
          childs.push(this.findNodeById(link.from))

        });

      let processed = childs.filter(node => node.satisfact !== '');

      return (processed.length === childs.length);
    },
    haveJustOneAnd(id) {
      let childs = this.getChildWithArrowAnd(id);
      return childs.length;

    },
    findByIdInArray(id, array) {
      return array.filter(array.id == id);
    },
    changeSatisfact(position){
      this.scene.nodes.map(node => {
          let links = this.findLinkByToId(node.id);
          //let children = this.findNodesChildren(this.findLinkByToId(node.id));

          links.map(link => {
            let child = this.findNodeById(link.from);
            
            if(link.type == "arrow-or") {
             if (this.allHaveStatus(node.id, "or")){
                if(this.haveJustOneSatisfact(node.id, "or")){
                  node.satisfact = "Satisficed"
                }else {
                  node.satisfact = "Denied"
                }
              } else {
                node.satisfact = '';
              }

            }

            if(link.type == "arrow-and") {
              if(this.haveJustOneAnd(node.id) === 1){
                alert('Não é possivel realizar uma avaliação com apenas uma ligaçãdo do tipo AND');
                return;
              }else if (this.allHaveStatus(node.id, "and")){
                if(this.allIsSatisfact(node.id, "and")){
                  node.satisfact = "Satisficed"
                }else {
                  node.satisfact = "Denied"
                }
              } else {
                node.satisfact = '';
              }
            }

          
            if(link.type == "arrow-make"){
              
              if(child.satisfact != 'Weakly Denied' && child.satisfact != 'Weakly' 
              && child.satisfact != "Denied" && child.satisfact != "Conflict" )
                  node.satisfact = child.satisfact;
              else
                  node.satisfact = '';
            }

            if(link.type == "arrow-break"){              
              let links = this.findLinkByToId(link.to);
              
              if(!(links.length > 1))              
                switch(child.satisfact) {
                  case 'Weakly Satisficed':
                    node.satisfact = 'Weakly Denied'
                  break;
                  case 'Satisficed':
                    node.satisfact = 'Denied'
                  break;

                  default:
                    node.satisfact = ''
                    break;
                }
              else {
                let nodes = [];
                links = links.filter(l => l.id != link.id);

                links.map(l => {
                  let scopeLink = this.findNodeById(l.from);
                  if(link.from != scopeLink.id)
                    nodes.push(scopeLink)
                })

                console.log(nodes, links)

                
              } 
            }

            if(link.type == 'arrow-help') {
              switch(child.satisfact) {
                case 'Satisficed':
                  node.satisfact = 'Weakly Satisficed'
                break;

                default:
                  node.satisfact = ''
                  break;
              } 
            }

             if(link.type == 'arrow-hurt') {
              switch(child.satisfact) {
                case 'Satisficed':
                  node.satisfact = 'Weakly Denied'
                break;

                default:
                  node.satisfact = ''
                  break;
              } 
            }
          
          })


      })
    }
  }
}
</script>

<style lang="scss">

body {
  margin: 0px;
}

.aside {
  width: 250px;
  height: 100%;
}
.aside-left {
  float: left;
  border-right: 1px solid black;
}

.aside-right {
  float: right;
  border-left: 1px solid black;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
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
