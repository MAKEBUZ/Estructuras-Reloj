<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";

export default defineComponent({
  setup() {
    const digitalTime = ref<string>("00:00:00");
    const angleHours = ref<number>(0);
    const angleMinutes = ref<number>(0);
    const angleSeconds = ref<number>(0);
    const points = ref<string>("");

    const hoursList = Array.from({ length: 12 }, (_, i) => i + 1); 
    const minutesList = Array.from({ length: 60 }, (_, i) => i);    
    const secondsList = Array.from({ length: 60 }, (_, i) => i);    

    let hourIndex = 0;
    let minuteIndex = 0;
    let secondIndex = 0;

    onMounted(() => {
      generatePoints();
      updateClock();
      setInterval(updateClock, 1000);
    });

    function generatePoints() {
      let html = `<circle cx="150" cy="150" r="5" stroke="transparent" fill="black" />`;
      for (let i = 1; i <= 12; i++) {
        const angle = (2 * Math.PI * i) / 12;
        const x = 120 * Math.cos(angle - Math.PI / 2) + 150;
        const y = 120 * Math.sin(angle - Math.PI / 2) + 150;
        html += `
          <text 
            x="${x}" 
            y="${y}" 
            fill="#1d1a2f" 
            font-size="20" 
            text-anchor="middle" 
            alignment-baseline="middle"
          >${i}</text>`;
      }
      points.value = html;
    }

    function updateClock() {
      secondIndex = (secondIndex + 1) % secondsList.length;
      if (secondIndex === 0) {
        minuteIndex = (minuteIndex + 1) % minutesList.length;
        if (minuteIndex === 0) {
          hourIndex = (hourIndex + 1) % hoursList.length;
        }
      }

      const hours = hoursList[hourIndex];
      const minutes = minutesList[minuteIndex];
      const seconds = secondsList[secondIndex];

      digitalTime.value = `${String(hours).padStart(2, "0")}:${String(minutes).padStart(2, "0")}:${String(seconds).padStart(2, "0")}`;
      angleHours.value = (360 * hours) / 12;
      angleMinutes.value = (360 * minutes) / 60;
      angleSeconds.value = (360 * seconds) / 60;
    }

    return {
      digitalTime,
      angleHours,
      angleMinutes,
      angleSeconds,
      points
    };
  }
});
</script>

<template>
  <div id="clock">
    <div id="background1"></div>
    <div id="digital-hour">
      <span id="digital-hour-text">{{ digitalTime }}</span>
    </div>
    <div id="clock-hands">
      <svg id="points" width="300" height="300" v-html="points"></svg>  
      <div id="hours" :style="{ transform: `rotate(${angleHours}deg)` }">
        <svg width="300" height="300">
          <line x1="150" y1="50" x2="150" y2="150" stroke="black" stroke-width="6" stroke-linecap="round" />
        </svg>
      </div>
      <div id="minutes" :style="{ transform: `rotate(${angleMinutes}deg)` }">
        <svg width="300" height="300">
          <line x1="150" y1="30" x2="150" y2="150" stroke="black" stroke-width="4" stroke-linecap="round" />
        </svg>
      </div>
      <div id="seconds" :style="{ transform: `rotate(${angleSeconds}deg)` }">
        <svg width="300" height="300">
          <line x1="150" y1="30" x2="150" y2="150" stroke="red" stroke-width="2" stroke-linecap="round" />
        </svg>
      </div>
    </div>
  </div>
</template>

<style lang="css">
* {
  box-sizing: border-box;
}

body {
  background-image: url("../assets/background2.jpeg");
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background-repeat: no-repeat;
  background-size: cover;
}

#clock,
#background1,
#digital-hour {
  width: 300px;
  height: 300px;
  color: black;
}

#background1 {
  background: linear-gradient(white, rgb(198, 198, 198));
  border-radius: 50%;
  border: 15px solid #d51111;
  filter: drop-shadow(0 10px 2px #660000);
}

#clock,
#clock-hands {
  position: relative;
}

#background1,
#hours,
#minutes,
#seconds,
#digital-hour,
#points {
  position: absolute;
}

#digital-hour {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 150px;
  font-size: 130%;
  opacity: 0.5;
}

#clock-hands {
  filter: drop-shadow(0 5px 2px #3f6d4e);
}

</style>
