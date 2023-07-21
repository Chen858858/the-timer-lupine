<template>
    <div class="timer-container">
        <Input v-model="modified_timer_name" @change="modify_timer()" font_size="4" placeholder="NAME" text_align="left" width="280" />
        <div class="time-container">            
            <div><Input v-model="modified_timer_time_set_hr" placeholder="00" /> hr</div>
            <div><Input v-model="modified_timer_time_set_mn" placeholder="00" /> mn</div>
            <div><Input v-model="modified_timer_time_set_sc" placeholder="00" /> sc</div>
        </div>
        <Progress />
        <div class="buttons-container">
            <Button v-if="!timer.time_set" @click="set_time()" icon="fa-solid fa-hourglass-start" :is_error="error_time"  value="Set time" width="180" />
            <template v-else>
                <Button icon="fa-solid fa-play" value="Start" width="180" />
                <Button icon="fa-solid fa-play" value="Resume" width="180" />
                <Button icon="fa-solid fa-pause" value="Pause" width="180" />
                <Button icon="fa-solid fa-bell-slash" value="Silence" width="180" />
                <Button icon="fa-solid fa-clock-rotate-left" is_outlined="true" value="Reset" width="180" />
            </template>
        </div>
        <Button @click="$emit('delete_timer', timer.key)" color="delete" icon="fa-solid fa-trash-can" width="180" value="Delete" />
    </div>
</template>

<script>
import Button from "./Button.vue";
import Input from "./Input.vue";
import Progress from "./Progress.vue";

export default {
    name: "Timer",
    components: {
        Button,
        Input,
        Progress
    },
    props: {
        "timer": {
            type: Object
        }
    },
    data(){
        return{
            error_time: false,
            modified_timer: {... this.timer},
            modified_timer_name: this.timer.name,
            modified_timer_time_current: this.timer.time_current,
            modified_timer_time_current_hr: this.timer.time_current_hr,
            modified_timer_time_current_mn: this.timer.time_current_mn,
            modified_timer_time_current_ms: this.timer.time_current_ms,
            modified_timer_time_current_sc: this.timer.time_current_sc,
            modified_timer_time_set_hr: this.timer.time_set_hr,
            modified_timer_time_set_mn: this.timer.time_set_mn,
            modified_timer_time_set_ms: this.timer.time_set_ms,
            modified_timer_time_set_sc: this.timer.time_set_sc
        }
    },
    methods: {
        check_integer(value){
            return !isNaN(value) && !isNaN(parseFloat(value)) && Number.isInteger(+value);
        },
        convert_ms_to_hrmnscms(ms_original){
            let ms_tracker = ms_original;
            let hrmnscms = {}
            hrmnscms.hr = Math.floor(ms_tracker / (60 * 60 * 1000));
            ms_tracker -= hrmnscms.hr * 60 * 60 * 1000;
            hrmnscms.mn = Math.floor(ms_tracker / (60 * 1000));
            ms_tracker -= hrmnscms.mn * 60 * 1000;
            hrmnscms.sc = Math.floor(ms_tracker / 1000);
            ms_tracker -= hrmnscms.sc * 1000;
            hrmnscms.ms = ms_tracker;
            return hrmnscms;
        },
        modify_timer(){
            console.log("naqui modify timer");
            this.$emit("modify_timer", {... this.timer,
                name: this.modified_timer_name,
                time_current: this.modified_timer_time_current,
                time_current_hr: this.modified_timer_time_current_hr,
                time_current_mn: this.modified_timer_time_current_mn,
                time_current_ms: this.modified_timer_time_current_ms,
                time_current_sc: this.modified_timer_time_current_sc,
                time_set: this.modified_timer_time_set,
                time_set_hr: this.modified_timer_time_set_hr,
                time_set_mn: this.modified_timer_time_set_mn,
                time_set_ms: this.modified_timer_time_set_ms,
                time_set_sc: this.modified_timer_time_set_sc
            });
        },
        set_time(){
            this.error_time = false;
            const errors_none_hr = this.check_integer(this.modified_timer_time_set_hr);
            const errors_none_mn = this.check_integer(this.modified_timer_time_set_mn);
            const errors_none_sc = this.check_integer(this.modified_timer_time_set_sc);
            const errors_none = errors_none_hr && errors_none_mn && errors_none_sc;
            console.log(`no errors in time: ${errors_none}`);
            if(errors_none){
                this.modified_timer_time_set =
                    this.modified_timer_time_set_hr * 60 * 60 * 1000 +
                    this.modified_timer_time_set_mn * 60 * 1000 +
                    this.modified_timer_time_set_sc * 1000
                ;
                const {hr, mn, ms, sc} = this.convert_ms_to_hrmnscms(this.modified_timer_time_set);
                console.log({hr, mn, ms, sc});
                this.modified_timer_time_set_hr = hr;
                this.modified_timer_time_set_mn = mn;
                this.modified_timer_time_set_ms = ms;
                this.modified_timer_time_set_sc = sc;
                this.modified_timer_time_current = this.modified_timer_time_set;
                this.modified_timer_time_current_hr = hr;
                this.modified_timer_time_current_mn = mn;
                this.modified_timer_time_current_ms = ms;
                this.modified_timer_time_current_sc = sc;
                this.modify_timer();
            }
            else{
                this.error_time = true;
                setTimeout(() => {
                    this.error_time = false;
                }, 1000);
            }
        }
    },
    emits: ["delete_timer", "modify_timer"]
}
</script>