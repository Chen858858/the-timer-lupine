<template>
  <button class="button-music">
    <div class="icon-container">
      <i class="fa-solid fa-music"></i>
    </div>
    <div class="value-container">
      {{audio_music_name}}
    </div>
  </button>
</template>

<script>
export default {
  name: "Button_Music",
  props: {
    audio_music_name: {
      type: String
    }
  }
}
</script>