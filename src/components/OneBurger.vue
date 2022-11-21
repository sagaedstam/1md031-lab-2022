<template>
    <div class="box">
      <h2 v-bind:key=burger.name>{{ burger.name }} <span class="thin">{{ burger.kCal }} kCal </span></h2>
      <img class="burger_img" v-bind:src="burger.url" alt="Burger">
      <h3>
        <ul>
        <li v-if="burger.lactose"> Contains <span class="allergies"> Lactose </span></li>
        <li v-if="burger.gluten"> Contains <span class="allergies"> Gluten </span></li>
      </ul>
      </h3>

      <p class="OrderStatus">
        <button v-on:click="decrease">-</button>
        {{amountOrdered}}
        <button v-on:click="increase">+</button>
      </p>

    </div>
  </template>
  
  <script>
  export default {
    name: 'Burger',
    props: {
      burger: Object
    },

    data: function() {
      return {
        amountOrdered: 0,
      }
    },

    methods: {
      increase: function(){
        this.amountOrdered++;
        this.$emit('Orderedburgers', {name: this.burger.name, amount: this.amountOrdered});
      },
      decrease: function(){
        if (this.burger.amountOrdered < 0){
          this.amountOrdered--;
        }
        this.$emit('Orderedburgers', {name: this.burger.name, amount: this.amountOrdered});
      }
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .burger_img{
    width: 250px;
    height: 125px;
    position: center;
    border-radius: 10px;
    padding: 25px;
}  
  </style>
  