<template>
  <button class="button-volume">
    <div class="icon-container">
      <i class="fa-solid fa-volume-off" style="margin-right: 2px;"></i>
      <div class="volume-bar height-1" v-if="volume > 0">&ensp;</div>
      <div class="volume-bar height-2" v-if="volume > 33">&ensp;</div>
      <div class="volume-bar height-3" v-if="volume > 66">&ensp;</div>
    </div>
    <div class="value-container">{{volume}}</div>
  </button>
</template>

<script>
export default {
  name: "Button_Volume",
  props: {
    volume: {
      default: 100,
      type: Number
    }
  }
}
</script>