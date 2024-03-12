<template>
  <div id="app">
    <div class="block-1">
      <h2>Add Elements</h2>
      <input v-model="newItem.name" placeholder="Name">
      <div class="slider-container">
        <input type="range" v-model.number="newItem.price" min="0" max="500" step="50" @input="updateSliderValue">
        <div class="slider-values">
          <div v-for="value in sliderValues" :key="value" :class="{ active: value === newItem.price }">{{ value }}</div>
        </div>
      </div>
      <button @click="addItem" :disabled="!newItem.name.trim() || newItem.price === 0">Add</button>
    </div>
    <div class="block-2">
      <h2>Elements List</h2>
      <ol>
        <li v-for="(item, index) in items" :key="index">
          {{ item.name }} - {{ item.price }}
          <button v-if="index !== items.length - 1" @click="deleteItem(index)">Delete</button>
        </li>
      </ol>
      <div v-if="items.length > 0" class="last-item-container">
        <h3>Last Item</h3>
        <div class="slider-container">
          <input type="range" v-model.number="lastItem.price" min="0" max="500" step="50" @input="updateLastItemPrice">
        </div>
        <p>{{ lastItem.name }} - {{ lastItem.price }}</p>
        <button @click="deleteItem(items.length - 1)">Delete</button>
      </div>
      <button @click="deleteAllItems">Delete All</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newItem: {
        name: '',
        price: 0
      },
      items: [],
      sliderValues: []
    }
  },
  created() {
    for (let i = 0; i <= 500; i += 50) {
      this.sliderValues.push(i);
    }
  },
  computed: {
    lastItem() {
      return this.items[this.items.length - 1];
    }
  },
  methods: {
    addItem() {
      if (this.newItem.name.trim() && this.newItem.price !== 0) {
        this.items.push({ ...this.newItem });
        this.newItem.name = '';
        this.newItem.price = 0;
      }
    },
    deleteItem(index) {
      this.items.splice(index, 1);
    },
    deleteAllItems() {
      this.items = [];
    },
    updateSliderValue() {
      const index = this.sliderValues.findIndex(value => value === this.newItem.price);
      if (index !== -1) {
        this.$el.querySelector('.slider-values div:nth-child(' + (index + 1) + ')').scrollIntoView({ behavior: 'smooth', block: 'nearest' });
      }
    },
    updateLastItemPrice() {
      const index = this.sliderValues.findIndex(value => value === this.lastItem.price);
      if (index !== -1) {
        this.$el.querySelector('.slider-values div:nth-child(' + (index + 1) + ')').scrollIntoView({ behavior: 'smooth', block: 'nearest' });
      }
    }
  }
}
</script>

<style>
#app{
  gap: 10px;
}

.block-1, .block-2 {
  width: 324px;
  height: 336px;
  background-color: #FAFAFA;
  color: #181818;
  border-radius: 12px;
  padding: 12px;
}

.slider-container {
  position: relative;
  display: flex;
  align-items: center;
}

.slider-values {
  position: absolute;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
  transition: all 1s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: auto;
  height: 25px;
  width: 50px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
}

.slider-values div {
  height: 25px;
  line-height: 25px;
  text-align: center;
  cursor: pointer;
}

.slider-values div.active {
  color: black;
}

.last-item-container {
  border-top: 1px solid #ccc;
  padding-top: 12px;
  margin-top: 12px;
}

button:disabled {
  cursor: not-allowed;
  opacity: 0.5;
}
</style>
