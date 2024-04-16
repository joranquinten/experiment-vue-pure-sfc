<script lang="ts">
import { defineComponent, ref } from "vue";
import type { Ref } from "vue";

const Counter = defineComponent({
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
export default Counter;

import { shallowMount } from "@vue/test-utils";
if (import.meta.vitest) {
  const { it, expect, describe } = import.meta.vitest;
  describe("Counter Pure SFC", (): void => {
    it("renders the initial count correctly", (): void => {
      const wrapper = shallowMount(Counter);
      expect(wrapper.find('[data-testid="count"]').text()).toBe("0");
    });

    it("increments the count when the increase button is clicked", async (): Promise<void> => {
      const wrapper = shallowMount(Counter);
      const increaseButton = wrapper.find('[data-testid="increase"]');
      await increaseButton.trigger("click");
      expect(wrapper.find('[data-testid="count"]').text()).toBe("1");
    });

    it("decrements the count when the decrease button is clicked and count is bigger than the min", async (): Promise<void> => {
      const wrapper = shallowMount(Counter);
      const decreaseButton = wrapper.find('[data-testid="decrease"]');
      await decreaseButton.trigger("click");
      expect(wrapper.find('[data-testid="count"]').text()).toBe("0");
    });

    it("decrements the count when the decrease button is clicked", async (): Promise<void> => {
      const wrapper = shallowMount(Counter, { props: { min: -10 } });
      const decreaseButton = wrapper.find('[data-testid="decrease"]');
      await decreaseButton.trigger("click");
      expect(wrapper.find('[data-testid="count"]').text()).toBe("-1");
    });

    it("resets the count when the reset button is clicked", async (): Promise<void> => {
      const wrapper = shallowMount(Counter);
      const increaseButton = wrapper.find('[data-testid="increase"]');
      const resetButton = wrapper.find('[data-testid="reset"]');
      await increaseButton.trigger("click");
      await resetButton.trigger("click");
      expect(wrapper.find('[data-testid="count"]').text()).toBe("0");
    });

    it("disables the increase button when the count reaches the max value", async (): Promise<void> => {
      const wrapper = shallowMount(Counter, {
        props: {
          max: 2,
        },
      });
      const increaseButton = wrapper.find('[data-testid="increase"]');
      await increaseButton.trigger("click");
      await increaseButton.trigger("click");
      expect(increaseButton.attributes()).toMatchObject({ disabled: "" });
    });

    it("disables the decrease button when the count reaches the min value", async (): Promise<void> => {
      const wrapper = shallowMount(Counter, {
        props: {
          min: -2,
        },
      });
      const decreaseButton = wrapper.find('[data-testid="decrease"]');
      await decreaseButton.trigger("click");
      await decreaseButton.trigger("click");
      expect(decreaseButton.attributes()).toMatchObject({ disabled: "" });
    });

    it("disables the reset button when the count is not dirty", (): void => {
      const wrapper = shallowMount(Counter);
      const resetButton = wrapper.find('[data-testid="reset"]');
      expect(resetButton.attributes()).toMatchObject({ disabled: "" });
    });

    it("enables the reset button when the count is dirty", async (): Promise<void> => {
      const wrapper = shallowMount(Counter);
      const increaseButton = wrapper.find('[data-testid="increase"]');
      const resetButton = wrapper.find('[data-testid="reset"]');
      await increaseButton.trigger("click");
      expect(resetButton.attributes()).not.toMatchObject({ disabled: "" });
    });
  });
}
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
