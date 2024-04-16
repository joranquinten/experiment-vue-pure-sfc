<script lang="ts">
import { defineComponent, ref } from "vue";
import type { Ref } from "vue";

export default defineComponent({
  name: "Counter",
  props: {
    min: {
      type: Number,
      default: 0,
    },
    max: {
      type: Number,
      default: 10,
    },
  },
  setup(props) {
    const isDirty: Ref<boolean> = ref(false);
    const count: Ref<number> = ref(0);

    const reset = (): void => {
      count.value = 0;
      isDirty.value = false;
    };

    const increment = (): void => {
      if (count.value < props.max) {
        count.value++;
        isDirty.value = true;
      }
    };

    const decrement = (): void => {
      if (count.value > props.min) {
        count.value--;
        isDirty.value = true;
      }
    };

    return {
      count,
      isDirty,
      reset,
      increment,
      decrement,
    };
  },
});
</script>

<template>
  <div class="card">
    <div class="count" data-testid="count">{{ count }}</div>
    <div>
      <button
        @click="increment"
        data-testid="increase"
        :disabled="count === max"
      >
        increase
      </button>
      <button @click="reset" data-testid="reset" :disabled="!isDirty">
        reset
      </button>
      <button
        @click="decrement"
        data-testid="decrease"
        :disabled="count === min"
      >
        decrease
      </button>
    </div>
  </div>
</template>

<style scoped></style>
