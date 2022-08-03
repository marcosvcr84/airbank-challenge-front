<template>
  <DataTable/>
</template>

<script lang="ts">
import Vue from 'vue'
import DataTable from '../components/DataTable.vue'

export default Vue.extend({
    name: "IndexPage",
    components: { DataTable },
})
</script>
