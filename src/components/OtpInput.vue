<template>
  <div class="otp-group">
    <input
      v-for="(digit, idx) in valueItems"
      :key="idx"
      ref="inputRefs"
      type="text"
      inputmode="numeric"
      autocomplete="one-time-code"
      pattern="\d{1}"
      :maxlength="valueLength"
      :class="['otp-input', { 'has-value': digit !== '' }]"
      :value="digit"
      @input="inputOnChange($event, idx)"
      @keydown="inputOnKeyDown($event, idx)"
      @focus="inputOnFocus($event, idx)"
    />
  </div>
</template>

<script setup>
import { ref, watch, nextTick, onMounted } from "vue";

const RE_DIGIT = /^\d+$/;

const props = defineProps({
  value: String,
  valueLength: Number,
  onChange: Function,
});

const valueItems = ref([]);
const inputRefs = ref([]);

watch(
  () => props.value,
  () => {
    generateValueItems();
  }
);

onMounted(() => {
  generateValueItems();
});

const generateValueItems = () => {
  const valueArray = props.value.split("");
  const items = [];

  for (let i = 0; i < props.valueLength; i++) {
    const char = valueArray[i];
    if (RE_DIGIT.test(char)) {
      items.push(char);
    } else {
      items.push("");
    }
  }

  valueItems.value = items;
};

const focusToNextInput = (idx) => {
  const nextInput = inputRefs.value[idx + 1];
  if (nextInput) {
    nextTick(() => {
      nextInput.focus();
    });
  }
};

const inputOnChange = (event, idx) => {
  const target = event.target;
  let targetValue = target.value.trim();
  const isTargetValueDigit = RE_DIGIT.test(targetValue);

  if (!isTargetValueDigit && targetValue !== "") {
    return;
  }

  targetValue = isTargetValueDigit ? targetValue : " ";

  const targetValueLength = targetValue.length;
  const isLastInput = idx === props.valueLength - 1;

  if (targetValueLength === 1) {
    const newValue =
      props.value.substring(0, idx) +
      targetValue +
      props.value.substring(idx + 1);

    props.onChange(newValue);

    if (!isTargetValueDigit) {
      return;
    }

    if (isLastInput) {
      // Fetch API here
      console.log("fetch API");
    } else {
      nextTick(() => {
        focusToNextInput(idx);
      });
    }
  } else if (targetValueLength === props.valueLength) {
    props.onChange(targetValue);
    target.blur();
  }
};

const inputOnKeyDown = (event, idx) => {
  const key = event.key;
  const target = event.target;

  if (key === "ArrowRight" || key === "ArrowDown") {
    event.preventDefault();
    focusToNextInput(idx);
  }

  if (key === "ArrowLeft" || key === "ArrowUp") {
    event.preventDefault();
    if (idx > 0) {
      inputRefs.value[idx - 1].focus();
    }
  }

  const targetValue = target.value;

  target.setSelectionRange(0, targetValue.length);

  if (key !== "Backspace" || targetValue !== "") {
    return;
  }

  if (idx > 0) {
    inputRefs.value[idx - 1].focus();
  }
};

const inputOnFocus = (event, idx) => {
  const target = event.target;

  const prevInput = inputRefs.value[idx - 1];
  if (prevInput && prevInput.value === "") {
    prevInput.focus();
  }

  target.setSelectionRange(0, target.value.length);
};
</script>

<style scoped>
.otp-group {
  display: flex;
  width: 100%;
  column-gap: 10px;
}

.otp-input {
  width: 48px;
  height: 60px;
  border: none;
  border-bottom: 3px solid #aaaaaa;
  border-bottom-left-radius: 2px;
  border-bottom-right-radius: 2px;
  margin-right: 20px;
  text-align: center;
  font-size: 32px;
  font-weight: bold;
  color: #303135;
  line-height: 1;
  padding: 0;
  box-sizing: border-box;
}

.otp-input:focus {
  outline: none;
}

.has-value {
  border-bottom-color: #0e4e9b;
}
</style>