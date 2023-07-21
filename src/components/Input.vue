<template>
    <input type="text" v-model="content" @input="handle_input" :class="['input-main', width != -1 ? `width-${width}` : '', font_size != -1 ? `font-size-${font_size}` : '', text_align ? `text-align-${text_align}` : '']" :placeholder="placeholder">
</template>

<script>
export default {
    name: "Input",
    props: {
        "font_size": {
            default: -1,
            type: Number
        },
        "placeholder": {
            default: "",
            type: String
        },
        "text_align": {
            default: null,
            type: String
        },
        "value": {
            default: "",
            type: String
        },
        "width": {
            default: -1,
            type: Number
        }
    },
    data(){
        return{
            content: this.value
        }
    },
    methods: {
        handle_input(e){
            this.$emit("input", this.content);
        }
    }
}
</script>