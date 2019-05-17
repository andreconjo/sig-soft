<template>
    <div>
        <h1>Toolbox</h1>

        <div class="">
            <div class="" v-for="(node, index) in nodes" :key="index" @click="addNode(node)">
                <div class="box">
                    <img :src="node.image" :alt="node.name">
                <div class="desc">
                    <p> {{ node.name }} </p>
                </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            nodes: [
                {
                    name: "softgoal",
                    image: require("../../assets/images/softgoal.png"),
                    type: "softgoal"
                },
                {
                    name: "action",
                    image: require("../../assets/images/action.png"),
                    type: "action"
                },
                {
                    name: "claim",
                    image: require("../../assets/images/claim.png"),
                    type: "claim"
                },
                {
                    name: "esquerda",
                    image: require("../../assets/images/seta-esquerda.png"),
                    type: "arrow-or"
                },
                {
                    name: "direita",
                    image: require("../../assets/images/seta-direita.png"),
                    type: "arrow-and"
                },
            ]
        }
    },
    
    methods: {
        addNode(node) {
            this.$emit('addNodeToolbox', node.type)
        }

    }
}
</script>

<style>
    img {
        height: 30px;
        width: 45px;
    }

    .box {
        float:left;
        cursor: pointer;
        width: 60px;
        height: 45px;
        text-align: center;
        margin-bottom: 20px;
    }

    .box .desc {
        margin-top: -15px;
    }
</style>
