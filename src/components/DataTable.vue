<template>
    <table ref="glitch" class="data-table glitch" title="Test">
      <tr class="row-border" v-for="(value, key) in data" :key="key">
        <td :class="{'align-left': true, 'title': true}">
          <div class="data-title">
            {{ key }}
          </div>
        </td>
        <td class="data">
          <ThresholdBar :field="key" :value="value"/>
        </td>
      </tr>

    </table>
    <svg height="0">
      <filter id="filter">
        <feColorMatrix type="matrix"
                       result="red_"
                       values="4 0 0 0 0
              0 0 0 0 0
              0 0 0 0 0
              0 0 0 1 0"/>
        <feOffset in="red_" dx="1" dy="0" result="red"/>
        <feColorMatrix type="matrix"
                       in="SourceGraphic"
                       result="blue_"
                       values="0 0 0 0 0
              0 3 0 0 0
              0 0 10 0 0
              0 0 0 1 0"/>
        <feOffset in="blue_" dx="-1" dy="0" result="blue"/>
        <feBlend mode="screen" in="red" in2="blue"/>
      </filter>
    </svg>
</template>

<script>
import {inject, onMounted, ref} from "vue";
import ThresholdBar from "@/components/ThresholdBar";
export default {
  name: "DataTable",
  components: {ThresholdBar},
  setup() {
    const data = inject("data");
    const glitch = ref();

    onMounted(() => {
      const before_element = glitch.value.cloneNode(true);
      before_element.style.position = "absolute";
      before_element.classList.add('animated-1')
      before_element.style.top = "0";
      before_element.style.textShadow = "-1px 0 red";
      before_element.style.background = "black";
      before_element.style.left = "2px";
      const after_element = glitch.value.cloneNode(true);
      after_element.style.position = "absolute";
      after_element.style.top = "0";
      after_element.style.left = "-2px";
      after_element.classList.add('animated-2')
      after_element.style.textShadow = "-1px 0 blue";
      after_element.style.background = "black";

      glitch.value.after(before_element)
      glitch.value.before(after_element)

    });
    return {
      data,
      glitch
    }
  }
}
</script>

<style scoped>
.align-left {
  text-align: left;
}

.row-border {
  width: 100%;
}

table, th, td {
  border-collapse: collapse;
  padding: 5px 3px;
  filter: url("#filter");
}

.title {
  width: 20%;
}

.data-table {
  width: 100%;
}

.data-title {
  color: orange;
  font-size: 4.25rem;
  font-variant: small-caps;
  text-align: right;
  padding-right: 1rem;
}

.data {
  width: 80%;
  max-height: 100px;
}

.glitch {
  position: relative;
}

@keyframes noise-anim-1 {
  0% {
    clip-path: inset(80% 0 45% 0);
  }
  20% {
    clip-path: inset(40% 0 70% 0);
  }
  40% {
    clip-path: inset(70% 0 20% 0);
  }
  60% {
    clip-path: inset(80% 0 58% 0);
  }
  80% {
    clip-path: inset(0% 0 95% 0);
  }
  100% {
    clip-path: inset(90% 0 10% 0);
  }
}

@keyframes noise-anim-2 {
  0% {
    clip-path: inset(40% 0 61% 0);
  }
  20% {
    clip-path: inset(92% 0 1% 0);
  }
  40% {
    clip-path: inset(43% 0 1% 0);
  }
  60% {
    clip-path: inset(25% 0 58% 0);
  }
  80% {
    clip-path: inset(54% 0 7% 0);
  }
  100% {
    clip-path: inset(0% 0 80% 0);
  }
}

.animated-1 {
  animation: noise-anim-1 .35s infinite linear alternate-reverse;
}
.animated-2 {
  animation: noise-anim-2 .35s infinite linear alternate-reverse;
}
</style>
