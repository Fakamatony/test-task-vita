<template>
  <div id="app">
    <div class="block-1">
      <VLogo/>
      <div class="slider-container">
        <p class="price">Установите цену товара:</p>
        <div class="roller">
          <div class="slider-values">
            <div v-for="value in sliderValues" :key="value" :class="{ active: value === newItem.price }">{{ value }}</div>
          </div>
          <p>₽</p>
        </div>

        <input type="range" v-model.number="newItem.price" min="0" max="500" step="50" @input="updateSliderValue">
      </div>
      <p class="price">Список добавленных товаров</p>
      <div class="name-container">

        <p class="counter">{{items.length + 1}}.</p>
        <input class="name-input" v-model="newItem.name" placeholder="Введите название">
        <div>{{newItem.price}} ₽</div>
      </div>
      <button class="blue-btn" @click="addItem" :disabled="!newItem.name.trim() || newItem.price === 0">
        <span class="add">Добавить товар</span>
        <img src="./components/icons/plus-icon.svg" alt="+">
      </button>
    </div>
    <div class="block-2">
      <VLogo/>
      <div v-if="items.length > 0" class="last-item-container">
        <p class="price">Установите цену товара:</p>
        <p>{{ lastItem.name }}</p>
        <div class="roller">
          <div class="slider-values">
            <div v-for="value in sliderValues" :key="value" :class="{ active: value === newItem.price }">{{ value }}</div>
          </div>
          <p>₽</p>
        </div>
        <div class="slider-container">
          <input type="range" v-model.number="lastItem.price" min="0" max="500" step="50" @input="updateLastItemPrice">
        </div>
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
import VLogo from "@/components/VLogo.vue";

export default {
  components: {VLogo},
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
  font-family: "Lato", sans-serif;
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
  .roller{
    display: flex;
    align-items: center;
    justify-content: center;
    gap:12px;

    :last-child{
      font-size: 32px;
    }
  }
  .slider-values {
    transition: all 1s ease;
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 25px;
    width: max-content;
    overflow: hidden;
    &>*{
      font-size: 32px;

    }
  }
}
.price{
  font-size: 15px;
  color: #78909C;
  font-weight: 500;
}
.name-container{
  display: flex;
  align-items: center;
  justify-content: space-between;
  .counter{
    font-size: 12px;
  }
  :last-child{
    padding: 2px 8px;
    border: 1px solid #00BCD4;
    border-radius: 6px;
    font-size: 12px;
    color: #78909C;
    font-weight: 300;
  }
}

.blue-btn{
  background: linear-gradient(180deg, #4FC3F7 0%, #2979FF 100%);
  width: 162px;
  height: 34px;
  outline: none;
  border: none;
  border-radius: 36px;
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  margin-left: auto;
  .add{
    color: #FFFFFF;
    font-size: 15px;
    font-weight: 300;
  }

}

.name-input{
  outline: none;
  border: 1px solid #00BCD4;
  border-radius: 6px;
  padding: 3px 6px;
  height: 24px;
  text-align: left;
  width: 237px;
  &::placeholder{
    font-family: "Lato", sans-serif;
    font-size: 15px;
    font-weight: 300;
    color: #78909C;
    line-height: 1;
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
