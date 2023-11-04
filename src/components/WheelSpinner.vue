<template>
  <div>
    <div v-for="item in wheelArray" :key="item.id">
      <h1 style="color:red">{{ item.name }}</h1>
    </div>
    <div v-for="prizes in removedItems" :key="prizes.id">
      <h1> {{ prizes.name }}</h1>
    </div>

    <button :disabled="isButtonDisabled" @click="generateRandomNumber()">Spin The Wheel!</button>
  </div>
</template>

<script>
import wheelData from '../../wheel-data.json';

export default {
  name: 'WheelSpinner',
  data() {
    return {
      randomNumber: 23,
      wheelArray: [],
      buttonClicked: false,
      currentDate: new Date().toLocaleDateString(),
      removedItems: []
    };
  },
  props: {},
  methods: {
    generateRandomNumber() {
      if (this.isButtonDisabled) {
        return;
      }
      var chosenOne = Math.floor(Math.random() * this.randomNumber);
      console.log(chosenOne, 'working',this.removedItems, this.randomNumber);
      
      // Find the index of the matching item in wheelArray
      const index = this.wheelArray.findIndex(item => item.id === chosenOne);
      
      if (index !== -1) {
        // If a match is found, remove the item from wheelArray and add it to removedItems
        const removedItem = this.wheelArray.splice(index, 1)[0];
        this.randomNumber -= 1;
        this.removedItems.push(removedItem);
        localStorage.setItem("removedItems", JSON.stringify(this.removedItems));
        localStorage.setItem("wheelArray", JSON.stringify(this.wheelArray));
        this.buttonClicked = true;
        this.isButtonDisabled = true;
      }
    },
    checkForNewDay() {
      const currentDate = new Date().toLocaleDateString();
      if (currentDate !== this.currentDate) {
        this.buttonClicked = false;
        this.isButtonDisabled = false;
        this.currentDate = currentDate;
      }
    },
  },
  computed: {
    isButtonDisabled: {
      get() {
        return localStorage.getItem('isButtonDisabled') === 'true';
      },
      set(value) {
        localStorage.setItem('isButtonDisabled', value);
      },
    }
  },
  mounted() {
    this.checkForNewDay();
    setInterval(this.checkForNewDay, 60000);
    const storedWheelArray = localStorage.getItem('wheelArray');
    if (storedWheelArray) {
      this.wheelArray = JSON.parse(storedWheelArray);
    } else {
      this.wheelArray = wheelData;
    }
  },
};
</script>
