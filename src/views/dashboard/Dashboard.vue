<template>
  <v-row>
    <v-col
      cols="24"
      md="8"
    >
      <dashboard-statistics-card></dashboard-statistics-card>
    </v-col>
  </v-row>
</template>

<script>

import DashboardStatisticsCard from "./DashboardStatisticsCard.vue"

export default {
  components: {
    DashboardStatisticsCard,
  },
  setup() {
    return {
    }
  },
}
</script>
