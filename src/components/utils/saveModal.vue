<template>

   <div class="modal">
       <div class="content">
           <p class="header bold">Configurações:</p>
           <div class="form">
            <span>Nome do catálogo:</span>
            <input v-model="scene.alias" type="text">
            <span>Conflita com quais softgoals? (separado por virgula):</span>
            <input v-model="scene.conflicts" type="text">
            </div>
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
            this.scene.conflicts = this.scene.conflicts.split(',');
            console.log(this.scene);
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

    .header {
        margin-top: 5px;
        font-size: 16px;
    }

    .bold {
        font-weight: bold;
    }

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

    .form {
        display: flex;
        flex-direction: column;
        padding: 10px;
    }

    .form span {
        margin-top: 5px;
    }

    .form input {
        display: block;
        width: 50%;
        margin: auto;
    }

</style>
