<template>

   <div class="modal">
       <div class="content">
         <simple-flowchart
            isModal
            :scene.sync="scene"
            :height="600"
            @setSelectedNodes="setSelectedNodes"
            @setSelectedLinks="setSelectedLinks"
        />
       <button @click="importScene">Importar</button>
       </div>
    </div>
</template>

<script>
    import SimpleFlowchart from '../SimpleFlowchart.vue'

export default {
    components: {
        SimpleFlowchart
    },
    data() {
        return {
            nodes: [],
            links: []
        };
    },
    props: [
        "scene"
    ],

    methods: {

        setSelectedLinks(links) {
            this.links = links;
        },

       setSelectedNodes(nodes) {
           this.nodes = nodes;
       },

       importScene() {
           this.$emit("importFromCatalog", this.nodes, this.links);
           this.closeModal()
       },

       closeModal() {
           this.$emit("closeModal")
       },

        pushLink() {

        },

        popLink() {

        }
    }

}
</script>

<style>

    .modal {
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, .2);
        position: absolute;
        z-index: 999;
    }

    .content {
        width: 600px;
        position: relative;
        margin: 10vh auto;
        background: white;

    }

</style>
