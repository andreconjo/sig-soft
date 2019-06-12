<template>

   <div class="modal">
       <div class="content">
         <simple-flowchart
            :scene.sync="scene"
            :height="600"
        />
       </div>
    </div>
</template>

<script>
    import SimpleFlowchart from '../SimpleFlowchart.vue'

export default {
    components: {
        SimpleFlowchart
    },
    data: ({
    }),
    props: [
        "scene"
    ]
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
