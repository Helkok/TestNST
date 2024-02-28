<template>
  <v-app>
    <v-main>
      <v-container style="margin-top: 24px">
        <MonthRangePicker
          v-model:start-date="startDate"
          v-model:end-date="endDate"
          :min-date="minDate"
          :max-date="maxDate"
        />

        <v-card style="margin-top: 24px">
          <v-card-text>
            <div>
              Limits: {{ renderDate(minDate) }} - {{ renderDate(maxDate) }}
            </div>
            <div>
              Selected: {{ renderDate(startDate) }} - {{ renderDate(endDate) }}
            </div>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import { ref } from "vue";
import MonthRangePicker from "./components/MonthRangePicker.vue";

const minDate = ref(new Date(2021, 1));
const maxDate = ref(new Date(2023, 6));

const startDate = ref(new Date(2022, 0));
const endDate = ref(new Date(2023, 0));

const renderDate = (date: Date) => {
  return `${date.getFullYear()}-${(date.getMonth() + 1)
    .toString()
    .padStart(2, "0")}`;
};
</script>
