<template>
  <button class="button-music test" @click="audio_play = !audio_play">
    <div class="icon-container">
      <i class="fa-solid fa-circle-play" v-if="!audio_play"></i>
      <i class="fa-solid fa-circle-pause" v-else></i>
    </div>
  </button>
  <audio :src="audio_music_source" v-if="audio_play" :volume="volume / 100" autoplay loop></audio>
</template>

<script>
export default {
  name: "Button_Music_Test",
  props: {
    audio_music_source: {
      type: String
    },
    volume: {
      type: Number
    }
  },
  data(){
    return{
      audio_play: false
    }
  }
}
</script>