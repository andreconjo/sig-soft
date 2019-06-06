<template>
    <div v-if="showContext" :style="menuStyle">
        <div class="contextbox" >
            <select name="label" v-model="satisfact">
                <option value="Denied">Denied</option>
                <option value="Weakly Denied">Weakly Denied</option>
                <option value="Weakly">Weakly</option>
                <option value="Weakly Satisficed">Weakly Satisficed</option>
                <option value="Satisficed">Satisficed</option>
                <option value="Conflict">Conflict</option>
            </select>

            <div class="priority">
                <label>Priority</label>
                <input type="checkbox" v-model="priority">
            </div>
            
            <div class="topic">
                <label>Topic</label>
                <input type="text" v-model="topic">
            </div>

            
        </div>
    </div>
</template>


<style lang="sass">
    .contextbox 
        width: 200px
        height: 100px
        background: white
        border: 1px solid black
        border-radius: 5px
        padding: 5px;

    .topic, .priority
        margin-top: 10px
        margin-bottom: 10px

    
</style>

<script>

export default {
    props: [
        'mouseEvent', 'satifactProp', 'priorityProp', 'topicProp'
    ],
    name: 'contextbox', 
    data() {
        return {
            showContext: true,
            menuStyle: null,
            satisfact: '',
            priority: false,
            topic: ''
        }
    },
    watch: {
        mouseEvent() {
            this.showContext = false;
            this.menuStyle = {
                left: this.mouseEvent.pageX + "px",
                top: this.mouseEvent.pageY + "px",
                position: "absolute",
                'z-index': "999"
            }
            this.$nextTick(() => {
                this.showContext = true;
            })
        },
        satisfact() {
            console.log('olar')
                this.changeProperties('satisfact', this.satisfact)
        },
        priority() {
            this.changeProperties('priority', this.priority)
        },
        topic() {
            this.changeProperties('topic', this.topic)
        }
    },
    methods: {
        changeProperties(property, value) {
            this.$emit('setAttributes', {property: property, value: value})
        }
    },
    mounted() {
        this.satisfact = this.satifactProp,
        this.priority = this.priorityProp,
        this.topic = this.topicProp
    }
}
</script>
