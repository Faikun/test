<template>
  <div class="background">
    <div class="container">
      <div class="stopwatches">
        <div class="stopwatches__item"
             v-for="stopwatch in stopwatches"
             :key="stopwatch.format"
             :class="stopwatch.running ? 'active' : ''"
        >
          <div class="stopwatches__time">{{ timerOutput(stopwatch) }}</div>
          <div class="stopwatches__item-control">
              <img v-if="!stopwatch.running" @click="start(stopwatch)" src="@/assets/images/play.png" alt="play">
              <img v-else class="stop" @click="stop(stopwatch)" src="@/assets/images/stop.png" alt="stop">
              <img :class="stopwatch.running ? 'white' : ''" @click="reset(stopwatch)" src="@/assets/images/reset.png" alt="reset">
          </div>
        </div>
        <div class="stopwatches__item last" @click="addStopWatch">
          <img class="stopwatches__item-add" src="@/assets/images/Plus.png" alt="addStopwatch">
        </div>
      </div><!-- ./stopwatches -->
    </div><!-- ./container -->
  </div><!-- ./background -->
</template>

<script>
import {reactive} from "vue";
export default {
  name: 'App',
  setup() {
    let stopwatches = reactive([
      {
        format: '00:00:00',
        running: false,
        stoppedDuration: 0,
        timeBegan: null,
        timeStopped: null,
        started: null
      }
    ]);

    let timerOutput = function (stopwatch) {
      if(stopwatch.format[3] === '0' && stopwatch.format[4] === '0') {
        return stopwatch.format.slice(6)
      }
      else if(stopwatch.format[0] === '0' && stopwatch.format[1] === '0') {
        return stopwatch.format.slice(3)
      }
      else {
        return stopwatch.format
      }
    }

    let addStopWatch = function () {
      stopwatches.push({
        format: '00:00:00',
        running: false,
        stoppedDuration: 0,
        timeBegan: null,
        timeStopped: null,
        started: null
      })
    }

    function start(stopwatch) {
        if(stopwatch.running) {
          return;
        }

        if (stopwatch.timeBegan === null) {
          reset(stopwatch);
          stopwatch.timeBegan = new Date();
        }

        if (stopwatch.timeStopped !== null) {
          stopwatch.stoppedDuration += new Date() - stopwatch.timeStopped;
        }

        stopwatch.started = setInterval(function () {clockRunning(stopwatch)}, 10);
        stopwatch.running = true;
    }

    function stop(stopwatch) {
      stopwatch.running = false;
      stopwatch.timeStopped = new Date();
      clearInterval(stopwatch.started);
    }

    function reset(stopwatch) {
      stopwatch.running = false;
      clearInterval(stopwatch.started);
      stopwatch.stoppedDuration = 0;
      stopwatch.timeBegan = null;
      stopwatch.timeStopped = null;
      stopwatch.format = '00:00:00';
    }

    function clockRunning(stopwatch) {
      let currentTime = new Date();
      let timeElapsed = new Date(currentTime - stopwatch.timeBegan - stopwatch.stoppedDuration);

      let hour = timeElapsed.getUTCHours(),
          min = timeElapsed.getUTCMinutes(),
          sec = timeElapsed.getUTCSeconds();

      stopwatch.format =
          zeroPrefix(hour, 2) + ":" +
          zeroPrefix(min, 2) + ":" +
          zeroPrefix(sec, 2);
    }

    function zeroPrefix(num, digit) {
      let zero = '';
      for(let i = 0; i < digit; i++) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    }

    return {
      stopwatches,
      start, stop, reset,
      addStopWatch,
      timerOutput
    }
  }
}
</script>
