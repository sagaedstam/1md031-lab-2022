<template>

  <body>
    <header>
      <div><img id="pic" src="https://images.photowall.com/products/47977/new-york-skyline-1.jpg?h=699&q=85"
          alt="header">
        <h1>Welcome to Burgers Online</h1>
      </div>
    </header>
    <main>
      <h2> This is where you execute burger selection </h2>
      <section class="burgers">

        <div class="wrapper">
          <Burger v-for="burger in burgers" v-bind:burger="burger" v-bind:key="burger.name"
            v-on:orderedBurgers="addToOrder($event)" />
        </div>
      </section>

      <h1>Customer information</h1>
      <p>
        <label for="firstname">Full name</label><br>
        <input type="text" id="firstlastname" v-model="fullname" required="required" placeholder="First- and Last name">
      </p>
      <p>
        <label for="email">E-mail</label><br>
        <input type="email" id="email" v-model="email" required="required" placeholder="Email">
      </p>
      <p>
        <label for="street">Street</label><br>
        <input type="text" id="street" v-model="streetname" required="required" placeholder="Street name">
      </p>
      <p>
        <label for="housenumber">House</label><br>
        <input type="text" id="housenumber" v-model="housenumber" required="required" placeholder="House number">
      </p>
      <p>
      <div id="mapbox">
        <div id="map" v-on:click="setLocation">
          click here
          <div id="dots">
            <div v-bind:style="{ 'left': location.x + 'px', 'top': location.y + 'px' }">
            </div>
          </div>
        </div>
      </div>
      </p>
      <p>
        <label for="payment">Payment Options</label><br>
        <select id="payment" v-model="pay">
          <option>Credit Card</option>
          <option>Swish</option>
          <option>Paypal</option>
        </select>
      </p>
      <p>
        <label for="gender">Gender</label><br>
        <input type="radio" id="gender1" v-model="gender" value="Male">
        <label for="gender1">Male</label><br>
        <input type="radio" id="gender2" v-model="gender" value="Female">
        <label for="gender2">Female</label><br>
        <input type="radio" id="gender3" v-model="gender" value="Non-binary">
        <label for="gender3">Non-binary</label><br>
        <input type="radio" id="gender4" v-model="gender" value="Undisclosed">
        <label for="gender4">Undisclosed</label><br>
      </p>

    </main>
    <div>
      <button id="orders" v-on:click="submit()">
        <img class="button_img"
          src="https://educaora.com/api/editors/6176786d68d9913b4bb2739c/published-files/image_button.png"
          alt="Send Info">
      </button>
    </div>
    <hr>
    <footer>&copy Edstam Inc.</footer>
  </body>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

/*function Menu_items (n,kC,pic,lact,glut) {
  this.name = n;
  this.kCal = kC;
  this.url = pic;
  this.lactose = lact;
  this.gluten = glut;
}

const Object = [new Menu_items('Barger','300',"https://static.cdn-expressen.se/images/ed/79/ed7990bd913c4ecaa07cdb9d76aef545/16x9/1920@80.jpg",'false','true'), 
new Menu_items('Birger','400',"https://cdn.shopify.com/s/files/1/0594/0409/1572/articles/Turkey-Burgers-with-Lemon-Mayo-Fire-and-Smoke-Society_1024x1024.jpg?v=1652363003",'true','true'), 
new Menu_items('Berger', '500',"https://assets.epicurious.com/photos/5c745a108918ee7ab68daf79/master/pass/Smashburger-recipe-120219.jpg",'false','false')];
console.log( Object );*/

export default {
  name: 'HomeView',
  components: {
    Burger
  },

  data: function () {
    return {
      burgers: menu,
      fullname: '',
      email: '',
      streetname: '',
      housenumber: '',
      location: { x: 0, y: 0 },
      pay: '',
      gender: '',
      orderedBurgers: {},

      //[ {name: "small burger", kCal: 250},
      //{name: "standard burger", kCal: 450},
      //{name: "large burger", kCal: 850}
      //]
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },

    submit: function () {
      console.log(this.fullname, this.email, this.streetname, this.housenumber, this.location, this.pay, this.gender, this.orderedBurgers);
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y },
        Name: this.fullname,
        Email: this.email,
        Street: this.streetname,
        HouseNumber: this.housenumber,
        Payment: this.pay,
        Gender: this.gender,
        orderItems: this.orderedBurgers
      });
    },

    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log("event:", event);
      console.log("event.fullname:", event.name);
      console.log("event.amount: " + event.amount);

      /*var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );*/
    },

    setLocation: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log("event.amount: " + event.amount);
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top
      };
      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      }
    }

  }
}

</script>

<style>
#dots {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}

#map {
  width: 1200px;
  height: 1000px;
  position: relative;
  background-image: url('../../public/img/polacks.jpg');
}

#mapbox {
  overflow: scroll;
  width: 1200px;
  height: 400px;
  border-style: solid;
}

#map>div {
  position: absolute;
  height: 20px;
  width: 20px;
  text-align: center;
}

div {
  padding: 10px 10px 10px 10px;
  margin: 10px 10px 10px 10px;
  border: dotted beige;
  background-color: white;
  opacity: 97%;
}

img {
  width: 250px;
  height: 125px;
  position: center;
  border-radius: 10px;
  padding: 25px;
}

.wrapper {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: 30% 30% 30%;
  background-color: #cccccc;
}

body {
  font-family: arial;
  margin: 50px;
  background-image: url("https://images.photowall.com/products/47977/new-york-skyline-1.jpg?h=699&q=85");
  background-color: #cccccc;
}

.box {
  border-radius: 10px;
  padding: 10px;
}

.burgers {
  font-weight: bold;
  color: blueviolet;
  padding: 10px 10px 50px 20px;
}

#burgers {
  text-transform: uppercase;
}

input[type="radio"] {
  height: .7em;
  width: .7em;
}

.head {
  margin: 30px 100px 30px 100px;
}

button:hover {
  background-color: grey;
}

.button_img {
  width: 100px;
  height: 60px;
}

h1 {
  color: blueviolet;
  border-radius: 10px;
}

h2 {
  color: beige;
  margin: 10px;
  padding: 5px;
  background-color: blueviolet;
  opacity: 80%;
  border-radius: 10px;
}

p {
  margin: 5px;
  padding: 5px;
  background-color: white;
  opacity: 70%;
}

header {
  overflow: hidden;
  margin: 50px 0 -60px 0;
  padding: 10px 10px 60px 0;
  text-align: center;
}

#pic {
  overflow: hidden;
  height: 175px;
  width: 1000px;
}
</style>