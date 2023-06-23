<template>
  <div v-if="field === 'temperature'">
    <div class="danger-span">
      <div class="point left-point">
        <div class="min">17&#8451;</div>
        <div class="tick"></div>
      </div>

      <span class="danger-text">danger</span>
      <div class="point caution-10-point mr-5">
        <div class="min">24&#8451;</div>
        <div class="tick"></div>
      </div>
    </div>
    <div class="point danger-point">
      <div class="min">25&#8451;</div>
      <div class="tick"></div>
    </div>
    <span class="danger-text">danger</span>
    <div class="point danger-10-point">
      <div class="min">26&#8451;</div>
      <div class="tick"></div>

    </div>
  </div>
  <div v-if="field === 'ph'">
    <div class="point left-point mr-60">
      <div class="min">1.0</div>
      <div class="tick"></div>
    </div>
    <div class="point caution-point">
      <div class="min">+6.5</div>
      <div class="tick"></div>
    </div>

    <span class="danger-text">caution</span>
    <div class="point caution-10-point mr-5">
      <div class="min">+7.0</div>
      <div class="tick"></div>
    </div>
    <div class="point danger-point">
      <div class="min">+7.0</div>
      <div class="tick"></div>
    </div>
    <span class="danger-text">danger</span>
    <div class="point danger-10-point">
      <div class="min">+10.0</div>
      <div class="tick"></div>

    </div>
  </div>
  <div v-if="field === 'tdsValue'">
    <div class="point left-point mr-60">
      <div class="min">0</div>
      <div class="tick"></div>
    </div>
    <div class="point caution-point">
      <div class="min">+0</div>
      <div class="tick"></div>
    </div>

    <span class="danger-text">caution</span>
    <div class="point caution-10-point mr-5">
      <div class="min">+10.0</div>
      <div class="tick"></div>
    </div>
    <div class="point danger-point">
      <div class="min">+16.0</div>
      <div class="tick"></div>
    </div>
    <span class="danger-text">danger</span>
    <div class="point danger-10-point">
      <div class="min">+20.0</div>
      <div class="tick"></div>

    </div>
  </div>
  <div class="progress" ref="progress">
    <div v-for="result in numberedArray" :key="result.idx" :class="{bar: true, 'filled': result.filled}">

    </div>

  </div>
</template>

<script>
import {computed, ref, toRef} from "vue";

export default {
  name: "ThresholdBar",
  props: ["field", "value"],
  setup(props) {
    const field = toRef(props, 'field');
    const value = toRef(props, 'value');
    const progress = ref();
    const bar = ref();
    const numberedArray = computed(() => {
      let cautionMin = 0, cautionMax = 0;
      let dangerMin = 0, dangerMax = 0;
      if (!progress.value)
        return;

      let numberOfBars = Math.floor(progress.value.clientWidth / 20);
      let percent;
      const cautionPercentage = 80 * 0.01;
      const dangerPercentage = 90 * 0.01;

      switch (field.value) {
        case "temperature":
          percent = (value.value - process.env.VUE_APP_TEMP_LOWER_LIMIT) / process.env.VUE_APP_TEMP_UPPER_LIMIT;
          dangerMin = process.env.VUE_APP_TEMP_LOWER_LIMIT;
          dangerMax = process.env.VUE_APP_TEMP_UPPER_LIMIT;
          cautionMin =process.env.VUE_APP_TEMP_LOWER_LIMIT * Math.pow(cautionPercentage, -1);
          cautionMax =process.env.VUE_APP_TEMP_UPPER_LIMIT * cautionPercentage;
          break;
        case "ph":
          percent = 6.00 /14;
          break;
        case "tdsValue":
          percent = (value.value - process.env.VUE_APP_TDS_LOWER_LIMIT) / process.env.VUE_APP_TDS_UPPER_LIMIT;
          break;
        default:
          percent = 0;
          break;
      }
      console.log(dangerPercentage, dangerMin, dangerMax, cautionMax, cautionMin)
      let filledBars = percent * numberOfBars;

      return Array(numberOfBars).fill(0, 0, numberOfBars).map((_, idx) => {
        return {filled: idx <= filledBars, idx}
      });
    });

    return {
      numberedArray,
      progress,
      bar
    }
  }
}
</script>

<style scoped>
.progress {
  height: 100px;
  width: 100%;
  display: flex;
  gap: 5px;
}

.bar {
  width: 15px;
  border-radius: 10px;
  height: 100px;
  background-color: gray;
}

.tick {
  background-color: orange;
  height: 15px;
  width: 4px;
}

.danger-text {
  text-transform: uppercase;
  font-weight: bold;
  color: orange;
  padding: 0 15px;
  text-align: center;
  vertical-align: bottom;
}

.min {
  color: orange;
}

.filled {
  background-color: blue;
}

.left-point {
}

.point {
  display: inline-block;
  max-width: 4px;
}

.mr-5 {
  margin-right: 100px;
}

.mr-60 {
  margin-right: 40%;
}

.danger-span {
  display: inline;
  margin: 0 15px;
}
</style>
