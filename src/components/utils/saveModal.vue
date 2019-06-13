<template>

   <div class="modal">
       <div class="content">
           <p>Nome para sua cena:</p>
            <input v-model="scene.alias" type="text">
            <button @click="salvar">salvar</button>
       </div>
    </div>
</template>

<script>

import axios from "axios"

export default {
    components: {
        
    },
    data() {
        return {
        };
    },
    props: [
        "scene"
    ],

    methods: {
        salvar() {
            console.log(this.scene)
             axios
            .post('http://localhost:8080/save', this.scene)
            .then(response => { 
                this.scenes = response.data
                })
            this.closeModal();
        },
        closeModal() {
            this.$emit("closeModal")
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
