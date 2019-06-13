<template>
    <div>
        <h1>Catalogo</h1>

        <div class="contens">
            <div class="items" v-for="(scene, index) in scenes" :key="index" @click="goModal(scene)">
                <div class="box catalog">
                <div class="desc">
                    <p> {{ scene.alias }} </p>
                </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import axios from "axios"

export default {
    data() {
        return {
            scenes: [
            ]
        }
    },
    mounted() {
        axios
            .get('http://localhost:8080/')
            .then(response => { 
                this.scenes = response.data
                })
    },

    methods: {
        goModal(scene) {
            this.$emit('openModal', scene)
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
        text-align: center;
        margin-bottom: 20px;
    }

    .box .desc {
        margin-top: -15px;
    }

    .items:not(:first-child) {
        margin-left: 80px;
    }


</style>
