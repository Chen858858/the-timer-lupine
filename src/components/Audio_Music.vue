<template>
    <audio :src="audio_music_source" :volume="volume / 100" autoplay loop></audio>
</template>

<script>
export default {
    name: "Audio_Music",
    props: {
        audio_music_source: {
            type: String
        },
        volume: {
            default: 100,
            type: Number
        }
    }
}
</script>