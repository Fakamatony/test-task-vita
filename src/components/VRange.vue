<template>
  <div class="range-input">
    <input
        type="range"
        :min="min"
        :max="max"
        :value="value"
        @input="updateValue"
        ref="rangeInput"
    >
    <div class="range-input__value">{{ value }}</div>
  </div>
</template>

<script>
export default {
  props: {
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    value: {
      type: Number,
      default: 0
    }
  },
  methods: {
    updateValue(event) {
      this.$emit('input', event.target.value);
    }
  },
  mounted() {
    this.$refs.rangeInput.style.backgroundSize = `calc(${(this.value - this.min) * 100 / (this.max - this.min)}% 100%)`;
  },
  watch: {
    value(newValue) {
      this.$refs.rangeInput.style.backgroundSize = `calc(${(newValue - this.min) * 100 / (this.max - this.min)}% 100%)`;
    }
  }
}
</script>

<style scoped>
.range-input {
  position: relative;
  width: 100%;
}

.range-input input[type="range"] {
  -webkit-appearance: none;
  width: 100%;
  height: 8px;
  border-radius: 4px;
  background: #d3d3d3;
  outline: none;
}

.range-input input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: #4CAF50;
  cursor: pointer;
}

.range-input input[type="range"]::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: #4CAF50;
  cursor: pointer;
}

.range-input__value {
  position: absolute;
  top: -20px;
  left: 0;
  width: 100%;
  text-align: center;
  font-size: 14px;
  font-weight: bold;
  color: #333;
}
</style>