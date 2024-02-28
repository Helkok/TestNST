<template>
  <div
    style="display: flex; flex-direction: row; width: 100%; align-items: end"
  >
    <div style="display: flex; flex-direction: column">
      <v-btn
        variant="text"
        style="display: flex"
        :active="displayMode === DisplayMode.Years"
        @click="displayMode = DisplayMode.Years"
      >
        Все года
      </v-btn>
      <v-btn
        variant="text"
        style="display: flex"
        :active="displayMode === DisplayMode.Months"
        @click="displayMode = DisplayMode.Months"
      >
        Месяцы
      </v-btn>
    </div>

    <div style="display: flex; width: 100%">
      <v-range-slider
        :model-value="[startValue, endValue]"
        :ticks="ticks"
        show-ticks="always"
        :step="1"
        :min="minValue"
        :max="maxValue"
        @update:model-value="onUpdate"
        thumb-label="always"
      >
        <template #thumb-label="{ modelValue }">
          {{ renderTooltip(modelValue) }}
        </template>
      </v-range-slider>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";

const props = defineProps<{
  minDate: Date;
  maxDate: Date;

  startDate: Date;
  endDate: Date;
}>();

const emit = defineEmits<{
  (event: "update:startDate", value: Date): void;
  (event: "update:endDate", value: Date): void;
}>();

const enum DisplayMode {
  Months,
  Years,
}

const displayMode = ref(DisplayMode.Years);

const getTotalMonths = (date: Date) => {
  return date.getFullYear() * 12 + date.getMonth();
};

const getDateByTotalMonths = (totalMonths: number) => {
  const year = Math.floor(totalMonths / 12);
  const month = totalMonths % 12;

  return new Date(year, month);
};

// Все значения слайдера, переведенные в месяцы относительно эпохи Unix
const minValue = computed(() => getTotalMonths(props.minDate));
const maxValue = computed(() => getTotalMonths(props.maxDate));
const startValue = computed(() => getTotalMonths(props.startDate));
const endValue = computed(() => getTotalMonths(props.endDate));

const onUpdate = ([start, end]: [number, number]) => {
  // Переводим значения слайдера обратно в даты
  emit("update:startDate", getDateByTotalMonths(start));
  emit("update:endDate", getDateByTotalMonths(end));
};

const monthTicks = [
  "янв",
  "фев",
  "мар",
  "апр",
  "май",
  "июн",
  "июл",
  "авг",
  "сен",
  "окт",
  "ноя",
  "дек",
];

// Подписи к слайдеру
// Показываются в зависимости от displayMode
const ticks = computed(() => {
  const result: Record<number, string> = {};

  // Показываем только годы
  if (displayMode.value === DisplayMode.Years) {
    for (let i = minValue.value; i <= maxValue.value; i++) {
      const date = getDateByTotalMonths(i);

      if (date.getMonth() === 0) {
        result[i] = date.getFullYear().toString();
      }
    }
  } else {
    // Показываем месяцы (где вместо января - год)

    for (let i = minValue.value; i <= maxValue.value; i++) {
      const date = getDateByTotalMonths(i);

      result[i] =
        date.getMonth() === 0
          ? date.getFullYear().toString()
          : monthTicks[date.getMonth()];
    }
  }

  return result;
});

const monthNames = [
  "Январь",
  "Февраль",
  "Март",
  "Апрель",
  "Май",
  "Июнь",
  "Июль",
  "Август",
  "Сентябрь",
  "Октябрь",
  "Ноябрь",
  "Декабрь",
];

// Всплывашки для слайдера
const renderTooltip = (value: number) => {
  const date = getDateByTotalMonths(value);

  return `${monthNames[date.getMonth()]} ${date.getFullYear()}`;
};
</script>
