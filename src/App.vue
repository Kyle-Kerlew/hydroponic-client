<template>
  <div v-if="data">
    <DataTable/>

  </div>
</template>

<script>
import {onBeforeUnmount, onMounted, provide, ref} from 'vue'
import DataTable from "@/components/DataTable";
import './styles/global.css';

export default {
  name: 'App',
  components: {DataTable},
  setup() {
    const data = ref();
    const pollInterval = ref();
    provide('data', data);
    const fetchData = async () => {
      data.value = await (await fetch(`http://${process.env.VUE_APP_CONNECTION_HOST}/water/health`)).json();
    }

    onMounted(async () => {
      await fetchData();
      pollInterval.value = setInterval(fetchData, process.env.VUE_APP_REFRESH_RATE * 1000);
    })

    onBeforeUnmount(() => {
      clearInterval(pollInterval.value);
    })

    return {
      data,
    }
  }
}
</script>

<style scoped>
</style>
