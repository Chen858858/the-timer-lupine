<template>
    <button v-if="icon.length == 0" :class="['button-main', is_outlined ? 'is-outlined' : '', width != -1 ? `width-${width}` : '', color ? `color-${color}`: '', is_error ? 'is-error' : '']">
        {{value}}
    </button>
    <button v-else :class="['button-main', is_outlined ? 'is-outlined' : '', width != -1 ? `width-${width}` : '', color ? `color-${color}`: '', is_error ? 'is-error' : '']">
        <div class="icon-container">
            <i :class="icon"></i>
        </div>
        <div class="value-container">
            {{value}}
        </div>
    </button>
</template>

<script>
export default {
    name: "Button",
    props: {
        color: {
            default: "",
            type: String
        },
        icon: {
            default: "",
            type: String
        },
        is_error: {
            default: false,
            type: Boolean
        },
        is_outlined: {
            default: false,
            type: Boolean
        },
        value: {
            default: "",
            type: String
        },
        width: {
            default: -1,
            type: Number
        }
    }
}
</script>