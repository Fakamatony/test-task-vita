<template>
  <div id="app">
    <div class="block-1">
      <h2>Add Elements</h2>
      <div class="slider-container">
        <p>Установите цену товара:</p>
        <div class="slider-values">
          <div v-for="value in sliderValues" :key="value" :class="{ active: value === newItem.price }">{{ value }}</div>
          <p>P</p>
        </div>
        <input type="range" v-model.number="newItem.price" min="0" max="500" step="50" @input="updateSliderValue">
      </div>
      <div class="name-container">
        <p class="counter">{{items.length + 1}}.</p>
        <input v-model="newItem.name" placeholder="Name">
        <div>{{newItem.price}} P</div>
      </div>
      <button @click="addItem" :disabled="!newItem.name.trim() || newItem.price === 0">Add</button>
    </div>
    <div class="block-2">
      <h2>Elements List</h2>
      <div v-if="items.length > 0" class="last-item-container">
        <h3>Last Item</h3>
        <div class="slider-container">
          <input type="range" v-model.number="lastItem.price" min="0" max="500" step="50" @input="updateLastItemPrice">
        </div>
        <p>{{ lastItem.name }} - {{ lastItem.price }}</p>
        <button @click="deleteItem(items.length - 1)">Delete</button>
      </div>
      <ol>
        <li v-for="(item, index) in items" :key="index">
          {{ item.name }} - {{ item.price }}
          <button v-if="index !== items.length" @click="deleteItem(index)">Delete</button>
        </li>
      </ol>
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
    const savedItems = JSON.parse(localStorage.getItem('items'));
    if (savedItems) {
      this.items = savedItems;
      this.newItem.price = savedItems[savedItems.length - 1]?.price || 0;
    }
  },
  watch: {
    // Сохранить значения при каждом изменении
    items: {
      handler(items) {
        localStorage.setItem('items', JSON.stringify(items));
      },
      deep: true
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
        const newItem = { ...this.newItem };
        this.items.push(newItem);
        this.newItem.name = '';
        this.newItem.price = 0;
        this.lastItem.price = newItem.price;
        this.updateSliderValue();
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
    updateLastItemPrice(index) {
      if (index !== undefined) {
        this.items[index].price = this.lastItem.price;
      }
      const index2 = this.sliderValues.findIndex(value => value === this.lastItem.price);
      if (index2 !== -1) {
        this.$el.querySelector('.slider-values div:nth-child(' + (index2 + 1) + ')').scrollIntoView({ behavior: 'smooth', block: 'nearest' });
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
  gap: 12px;
  display: flex;
  flex-direction: column;
}

.slider-container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  .slider-values {
    transition: all 1s ease;
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 25px;
    width: 50px;
    overflow: hidden;
  }
}

.name-container{
  display: flex;
  gap: 12px;
  .counter{
    padding: 3px 8px;
  }
  :last-child{
    padding: 3px 8px;
    border: #181818 solid 1px;
  }
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

button:disabled {
  cursor: not-allowed;
  opacity: 0.5;
}

.block-2 .slider-values {
  display: none;
}
</style>
