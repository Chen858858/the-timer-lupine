<template>    
    <div class="timer-container">
        <Input v-model="modified_timer.name" @change="modify_timer()" font_size="4" placeholder="NAME" text_align="left" width="280" />
        <div :class="['time-container', timer.timer_finished ? 'is-done': '']">            
            <template v-if="timer.time_set == 0">
                <div><Input v-model="modified_timer_time_set_hr_raw" placeholder="0" :value="timer.time_set_hr" /> hr</div>
                <div><Input v-model="modified_timer_time_set_mn_raw" placeholder="0" :value="timer.time_set_mn" /> mn</div>
                <div><Input v-model="modified_timer_time_set_sc_raw" placeholder="0" :value="timer.time_set_sc" /> sc</div>
            </template>
            <template v-else>
                <div>{{timer.time_left_hr}} hr</div>
                <div>{{timer.time_left_mn}} mn</div>
                <div>{{timer.time_left_sc}} sc</div>
            </template> 
        </div>
        <div class="notification-container" v-if="timer.timer_finished">
            <div class="icon-container">
                <i class="fa-solid fa-hourglass-end"></i>
            </div>
            Timer finished
            {{timer_finished_ago_hr}} hr
            {{timer_finished_ago_mn}} mn
            {{timer_finished_ago_sc}} sc ago.
        </div>
        <div class="notification-container is-clickable" v-if="warning_capped" @click="warning_capped = false">
            <div class="icon-container">
                <i class="fa-solid fa-warning"></i>
            </div>
            Inputted time exceeded maximum limit, set time capped. Click to dismiss.
        </div>
        <div class="notification-container" v-if="audio_music_chosen_description_show">
            <div class="icon-container">
                <i class="fa-solid fa-music"></i>
            </div>
            <a :href="audio_music_chosen_information.webpage" target="_blank">{{audio_music_chosen_description}}</a>
        </div>
        <div class="progress-bar" v-if="!timer.time_set || timer.time_left > 0">
            <div class="inner" :style="{'width': `${timer.time_set != 0 ? 100 * timer.time_left / timer.time_set : 100}%`}"></div>
        </div>
        <div class="buttons-container">
            <Button v-if="timer.time_set == 0" @click="set_time()" icon="fa-solid fa-hourglass-start" :is_error="error_time"  value="Set time" width="180" />
            <template v-else>
                <Button v-if="!timer.timer_started" @click="timer_start()" icon="fa-solid fa-play" value="Start" width="180" />
                <Button v-if="timer.timer_started && timer.timer_paused" @click="timer_resume()" icon="fa-solid fa-play" value="Resume" width="180" />
                <Button v-if="timer.timer_started && !timer.timer_paused && !timer.timer_finished" @click="timer_pause()" icon="fa-solid fa-pause" value="Pause" width="180" />
                <Button v-if="audio_on" @click="audio_on = false" icon="fa-solid fa-bell-slash" value="Silence" width="180" />
                <Button v-if="timer.timer_started" @click="timer_reset()" icon="fa-solid fa-clock-rotate-left" is_outlined="true" value="Reset" width="180" />
            </template>
        </div>
        <div class="buttons-container">
            <Button_Volume @click="audio_volume > 0 ? audio_volume -= 20 : audio_volume = 100" :volume="audio_volume" />
            <Button_Music @click="music_change()" :audio_music_name="audio_music_chosen_information.name" />
            <Button_Music_Test :audio_music_source="audio_music_chosen_information.source" :volume="audio_volume" />
            <Audio_Music v-if="audio_on" :audio_music_source="audio_music_chosen_information.source" :volume="audio_volume" />
        </div>
        <Button @click="$emit('delete_timer', timer.key)" color="delete" icon="fa-solid fa-trash-can" width="180" value="Delete" />
    </div>
</template>

<script>
import Audio_Music from "./Audio_Music.vue";
import Button from "./Button.vue";
import Button_Music from "./Button_Music.vue";
import Button_Music_Test from "./Button_Music_Test.vue";
import Button_Volume from "./Button_Volume.vue";
import Input from "./Input.vue";

export default {
    name: "Timer",
    components: {
        Audio_Music,
        Button,
        Button_Music,
        Button_Music_Test,
        Button_Volume,
        Input
    },
    props: {
        timer: {
            type: Object
        }
    },
    data(){
        return{
            audio_on: false,
            audio_music_chosen_index: -1,
            audio_music_chosen_information: {},
            audio_music_chosen_description: "",
            audio_music_chosen_description_show: true, // Decided to always show music description, just leave it here.
            audio_music_list: [
                {credits: "by eliot-beats from freesound", name: "Beats", source: "https://cdn.freesound.org/previews/544/544616_11865776-lq.mp3", webpage: "https://freesound.org/people/eliot-beats/sounds/544616/"},
                {credits: "by tec_studio from freesound", name: "Bell", source: "https://cdn.freesound.org/previews/361/361496_1431924-lq.mp3", webpage: "https://freesound.org/people/tec_studio/sounds/361496/"},
                {credits: "by BeeProductive from freesound", name: "Chopsticks", source: "https://cdn.freesound.org/previews/430/430049_4298084-lq.mp3", webpage: "https://freesound.org/people/BeeProductive/sounds/430049/"},
                {credits: "by reecord2 from freesound", name: "Beeps", source: "https://cdn.freesound.org/previews/96/96063_704006-lq.mp3", webpage: "https://freesound.org/people/reecord2/sounds/96063/"},
                {credits: "by Meral from freesound", name: "Piano", source: "https://cdn.freesound.org/previews/104/104587_1761703-lq.mp3", webpage: "https://freesound.org/people/Meral/sounds/104587/"},
                {credits: "by ALLANZ10D from freesound", name: "Ocean", source: "https://cdn.freesound.org/previews/235/235428_2425857-lq.mp3", webpage: "https://freesound.org/people/ALLANZ10D/sounds/235428/"},
                {credits: "by alpersez from freesound", name: "Guitar", source: "https://cdn.freesound.org/previews/416/416631_5701165-lq.mp3", webpage: "https://freesound.org/people/alpersez/sounds/416631/"},
                {credits: "by Breviceps from freesound", name: "Dialing", source: "https://cdn.freesound.org/previews/466/466832_9159316-lq.mp3", webpage: "https://freesound.org/people/Breviceps/sounds/466832/"}
            ],
            audio_volume: 100,
            audio_volume_multiplier: 1,
            error_time: false,
            interval_current: "",
            modified_timer: {... this.timer},
            modified_timer_time_set_hr_raw: this.timer.time_set_hr,
            modified_timer_time_set_mn_raw: this.timer.time_set_mn,
            modified_timer_time_set_sc_raw: this.timer.time_set_sc,
            timer_finished_ago_hr: 0,
            timer_finished_ago_mn: 0,
            timer_finished_ago_ms: 0,
            timer_finished_ago_sc: 0,
            update_gap: 1,
            warning_capped: false
        }
    },
    methods: {
        check_integer(value){
            return !isNaN(value) && !isNaN(parseFloat(value)) && Number.isInteger(+value);
        },
        convert_hrmnmssc_to_ms({hr, mn, ms, sc}){
            return hr * 60 * 60 * 1000 + mn * 60 * 1000 + sc * 1000 + ms * 1;
        },
        convert_ms_to_hrmnmssc(ms_original){
            let ms_tracker = ms_original;
            let hrmnmssc = {}
            hrmnmssc.hr = Math.floor(ms_tracker / (60 * 60 * 1000));
            ms_tracker -= hrmnmssc.hr * 60 * 60 * 1000;
            hrmnmssc.mn = Math.floor(ms_tracker / (60 * 1000));
            ms_tracker -= hrmnmssc.mn * 60 * 1000;
            hrmnmssc.sc = Math.floor(ms_tracker / 1000);
            ms_tracker -= hrmnmssc.sc * 1000;
            hrmnmssc.ms = ms_tracker;
            return hrmnmssc;
        },
        modify_timer(){
            this.$emit("modify_timer", this.modified_timer);
        },
        music_change(){
            if(this.audio_music_chosen_index != -1){
                this.audio_music_chosen_index = this.audio_music_chosen_index == 0 ? this.audio_music_list.length - 1 : this.audio_music_chosen_index - 1;
            }
            else{
                this.audio_music_chosen_index = Math.floor(Math.random() * this.audio_music_list.length);
            }
            this.audio_music_chosen_information = {...this.audio_music_list[this.audio_music_chosen_index]};
            this.audio_music_chosen_description = `"${this.audio_music_chosen_information.name}" ${this.audio_music_chosen_information.credits}`;
            this.audio_music_chosen_description_show = true;
        },
        set_left_hrmnmssc(ms_original){
            const {hr, mn, ms, sc} = this.convert_ms_to_hrmnmssc(ms_original);
            this.modified_timer = { ... this.modified_timer,
                time_left_hr: hr,
                time_left_mn: mn,
                time_left_ms: ms,
                time_left_sc: sc
            };
        },
        set_time(){
            this.error_time = false;
            const errors_none_hr = this.check_integer(this.modified_timer_time_set_hr_raw);
            const errors_none_mn = this.check_integer(this.modified_timer_time_set_mn_raw);
            const errors_none_sc = this.check_integer(this.modified_timer_time_set_sc_raw);
            let errors_none = errors_none_hr && errors_none_mn && errors_none_sc;
            if(errors_none){
                errors_none = errors_none && (
                    this.convert_hrmnmssc_to_ms({
                        hr: this.modified_timer_time_set_hr_raw,
                        mn: this.modified_timer_time_set_mn_raw,
                        ms: 0,
                        sc: this.modified_timer_time_set_sc_raw
                    })
                    > 0
                );
            }
            if(errors_none){
                this.modified_timer.time_set =
                    this.convert_hrmnmssc_to_ms({
                        hr: this.modified_timer_time_set_hr_raw,
                        mn: this.modified_timer_time_set_mn_raw,
                        ms: 0,
                        sc: this.modified_timer_time_set_sc_raw
                    });
                this.warning_capped = this.modified_timer.time_set > Number.MAX_SAFE_INTEGER;
                if(this.warning_capped){
                    this.modified_timer.time_set = Number.MAX_SAFE_INTEGER;
                }
                const {hr, mn, ms, sc} = this.convert_ms_to_hrmnmssc(this.modified_timer.time_set);
                this.modified_timer = {... this.modified_timer,
                    time_set_hr: hr,
                    time_set_mn: mn,
                    time_set_sc: sc,
                    time_left: this.modified_timer.time_set
                };
                this.set_left_hrmnmssc(this.modified_timer.time_left);
                this.modify_timer();
            }
            else{
                this.error_time = true;
                setTimeout(() => {
                    this.error_time = false;
                }, 1000);
            }
        },
        timer_finishing(){
            const timer_finished_ago = Date.now() - this.modified_timer.timer_finish_time;
            const {hr, mn, ms, sc} = this.convert_ms_to_hrmnmssc(timer_finished_ago);
            this.timer_finished_ago_hr = hr;
            this.timer_finished_ago_mn = mn;
            this.timer_finished_ago_sc = sc;
        },
        timer_pause(){
            clearInterval(this.interval_current);
            this.modified_timer = {... this.modified_timer,
                timer_paused: true,
                timer_paused_time: Date.now()
            };
            this.modify_timer();
        },
        timer_reset(){
            const modified_timer_previous = {... this.modified_timer};
            clearInterval(this.interval_current);
            this.modified_timer = {... this.modified_timer,
                timer_started: false,
                timer_paused: false,
                timer_paused_time: -1,
                timer_finished: false,
                timer_finish_time: -1,
                time_left: this.modified_timer.time_set
            };
            this.audio_on = false;
            this.set_left_hrmnmssc(this.modified_timer.time_left);
            this.timer_finished_ago_hr, this.timer_finished_ago_mn, this.timer_finished_ago_sc = 0;
            this.modify_timer();
            if(modified_timer_previous.time_left > 0 && !modified_timer_previous.timer_paused){
                this.timer_start();
            }
        },
        timer_resume(){
            this.modified_timer.timer_paused = false;
            this.modified_timer.timer_finish_time += Date.now() - this.modified_timer.timer_paused_time;
            this.modified_timer.timer_paused_time = -1;
            this.modify_timer();
            this.interval_current = setInterval(this.timer_running, this.update_gap);
        },
        timer_running(){
            this.modified_timer.time_left = this.modified_timer.timer_finish_time - Date.now();
            if(this.modified_timer.time_left <= 0){
                clearInterval(this.interval_current);
                this.modified_timer = { ... this.modified_timer,
                    timer_finished: true,
                    time_left: 0
                };
                this.set_left_hrmnmssc(this.modified_timer.time_left);
                this.modify_timer();
                this.audio_volume_multiplier = 1;
                this.audio_on = true;
                this.interval_current = setInterval(this.timer_finishing, this.update_gap * 10);
                return;
            }
            this.set_left_hrmnmssc(this.modified_timer.time_left);
            this.modify_timer();
        },
        timer_start(){
            this.modified_timer.timer_finish_time = Date.now() + this.modified_timer.time_set;
            this.modified_timer.time_left = this.modified_timer.time_set;
            this.set_left_hrmnmssc(this.modified_timer.time_left);
            this.modified_timer.timer_started = true;
            this.modify_timer();
            this.interval_current = setInterval(this.timer_running, this.update_gap);
        }
    },
    created(){
        this.music_change();
    },
    emits: ["delete_timer", "modify_timer"]
}
</script>