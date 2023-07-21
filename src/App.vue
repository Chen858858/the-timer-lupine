<!-- Run dev server with "npm run serve". -->

<template>
  <div class="timers-container">
    <Button @click="add_timer(0)" color="new" icon="fa-solid fa-plus" value="New timer" width="180" />
    <!--<Button v-if="timers.length == 0" @click="add_timer(false)" color="new" icon="fa-solid fa-plus" value="New timer" width="180" />-->
    <div v-for="(timer, idx) in timers" :key="timer.key">
      <!-- Button v-if="idx == 0" @click="add_timer(0)" color="new" icon="fa-solid fa-plus" value="New timer" width="180" / -->
      <Timer @delete_timer="delete_timer" @modify_timer="modify_timer" :key="timer.key" :timer="timer" />
      <Button @click="add_timer(idx + 1)" color="new" icon="fa-solid fa-plus" value="New timer" width="180" />
    </div>
    <!--
    <Button v-if="timers.length > 0" @click="add_timer(false)" color="new" icon="fa-solid fa-arrow-up" value="New timer" width="180" />
    <Timer v-for="timer in timers" @delete_timer="delete_timer" @modify_timer="modify_timer" :key="timer.key" :timer="timer" />
    <Button @click="add_timer(true)" color="new" icon="fa-solid fa-arrow-down" value="New timer" width="180" />
    -->
    <br><br>
    
    <Button width="300" color="info" icon="fa-solid fa-circle-question" :value="'How to use ' + (!how_to_use_show ? '(+)' : '(&times;)')" @click="how_to_use_show = !how_to_use_show" />
    <div v-if="how_to_use_show" style="font-size: 16pt;">
      <h1>How to use</h1>
      <ul>
        <li>Best user experience on laptops/desktops.</li>
        <li>For a new timer, click <div class="button-fake">NEW TIMER</div>.</li>
        <li>
          To set the time, input values for hour (hr), minute (mn), and second (sc).
          Click <div class="button-fake"><i class="fa-solid fa-hourglass-start"></i> SET TIME</div>.
          <br>If the time inputted is invalid (less than or equal to 0), the <div class="button-fake"><i class="fa-solid fa-hourglass-start"></i> SET TIME</div> button will shake.
        </li>
        <li>
          To start the timer, click <div class="button-fake"><i class="fa-solid fa-play"></i> START</div>.
          To pause the timer, click <div class="button-fake"><i class="fa-solid fa-pause"></i> PAUSE</div>.
          If the timer is paused, to resume, click <div class="button-fake"><i class="fa-solid fa-play"></i> RESUME</div>.
          To reset the timer, click <div class="button-fake"><i class="fa-solid fa-clock-rotate-left"></i> RESET</div>.
        </li>
        <li>
          After the timer is finished, to silence the alarm, click <div class="button-fake"><i class="fa-solid fa-bell-slash"></i> SILENCE</div>.
          To reset the timer, click <div class="button-fake"><i class="fa-solid fa-clock-rotate-left"></i> RESET</div>.
        </li>
        <li>To change the volume of the alarm sound, click on the volume icon with the volume next to it <div class="button-fake"><i class="fa-solid fa-volume-high"></i> (VOLUME)</div>.</li>
        <li>To change the alarm music, click on the music icon with the music name next to it <div class="button-fake"><i class="fa-solid fa-music"></i> (MUSIC NAME)</div>.</li>
        <li>
          To test the music and volume, click <div class="button-fake"><i class="fa-solid fa-play-circle"></i></div>.
          To end the test, click <div class="button-fake"><i class="fa-solid fa-pause-circle"></i></div>
        </li>
        <li>To delete a timer, click <div class="button-fake"><i class="fa-solid fa-trash-can"></i> DELETE</div></li>
      </ul>
    </div>

    <br><br>
    <Button width="300" color="development" icon="fa-solid fa-bolt" :value="'Development info ' + (!development_info_show ? '(+)' : '(&times;)')" @click="development_info_show = !development_info_show" />
    <div v-if="development_info_show" style="font-size: 16pt;">
      <h1>Development info</h1>
      <h2>General</h2>
      <ul>
        <li>Created and maintained by Junchen Wang (project's GitHub).</li>
        <li>Initial release July 2023.</li>
      </ul>
      <h2>Tech used</h2>
      <ul>
        <li><i class="fa-brands fa-js" style="color: rgb(247, 223, 30);"></i> JavaScript with the <a href="https://vuejs.org/" target="_blank"><i class="fa-brands fa-vuejs" style="color: rgb(65, 184, 131);"></i> Vue.js</a> framework.</li>
        <li><i class="fa-brands fa-css3-alt" style="color: rgb(38, 77, 228);"></i> CSS compiled with <a href="https://sass-lang.com/" target="blank"><i class="fa-brands fa-sass" style="color: rgb(204, 102, 153);"></i> Sass</a> (.scss).</li>
        <li><i class="fa-brands fa-html5" style="color: rgb(240, 101, 41);"></i> HTML.</li>
        <li><a href="https://fonts.google.com/" target="_blank">Google Fonts</a> with the <a href="https://fonts.google.com/specimen/Work+Sans" target="_blank">Work Sans</a> font.</li>
        <li><a href="https://fontawesome.com/" target="_blank"><i class="fa-brands fa-font-awesome" style="color: rgb(83, 141, 215);"></i> Font Awesome</a> icons.</li>
        <li><a href="https://freesound.org/" target="blank">Freesound</a> for the alarm music.</li>
      </ul>
    </div>
  </div>
</template>

<script>
import Button from "./components/Button.vue";
import Timer from "./components/Timer.vue";

export default {
  name: "App",
  components: {
    Button,
    Timer
  },
  data(){
    return{
      development_info_show: false,
      how_to_use_show: false,
      next_key: 0,
      timer_template: {
        key: null,
        name: "",
        time_left: 0,
        time_left_hr: 0,
        time_left_mn: 0,
        time_left_ms: 0,
        time_left_sc: 0,
        time_set: 0,
        time_set_hr: 0,
        time_set_mn: 0,
        time_set_sc: 0,
        timer_finish_time: 0,
        timer_finished: false,
        timer_started: false,
        timer_paused: false,
        timer_paused_time: 0
      },
      timers: []
    }
  },
  methods: {
    add_timer(idx){
      this.timers.splice(idx, 0, {... this.timer_template, key: this.next_key});
      this.next_key++;
    },
    delete_timer(key){
      this.timers.splice(this.timers.findIndex(timer => timer.key == key), 1);
    },
    modify_timer(modified_timer){
      this.timers[this.timers.findIndex(timer => timer.key == modified_timer.key)] = {... modified_timer};
    },
    update_title(){
      let title_text = "";
      this.timers.forEach(timer => {
        if(timer.timer_started && !timer.timer_paused){
          title_text += (timer.name.trim() ? timer.name : "Unnamed") + " ";
          if(timer.time_left <= 0){
            title_text += "(Finished)";
          }
          else{
            if(timer.time_left >= 3600000){
              title_text += `${timer.time_left_hr}hr `;
            }
            if(timer.time_left >= 60000){
              title_text += `${timer.time_left_mn}mn `;
            }
            title_text += `${timer.time_left_sc}sc`;
          }
          title_text += " | ";
        }
      });
      title_text += "Lupine Timer";
      document.title = title_text;
    }
  },
  created(){
    setInterval(this.update_title, 1);
  }
}
</script>

<link rel="stylesheet" src="./assets/style.css">