<template>

 <header>
            <img src="/img/bakgrund_header.jpg">
            <h1>Välkommen till Emmas Hamburgare!</h1>
        </header>
        <main>
            <section id="meny">
                    <h2>Välj din hamburgare</h2>
                        <p>Välj den hamburgare som du vill beställa:</p>
                        <div class="wrapper">
                          <Burger
                            v-for="currentBurger in burgers" 
                              v-bind:key="currentBurger.name" 
                              v-bind:burger="currentBurger"
                              class="Burger"
                              v-on:orderedBurger="addToOrder($event)"/>
                        </div>
                        
            </section>
            <section id="information">
                <br/>
                    <h2>Kundinformation</h2>
                        <p>Ange nödvändig information här.</p>
                        <form>
                        <h3>Leveransinformation:</h3>
                            <p>
                                <label for="name">Fullständigt namn</label><br>
                                <input type="text" id="name" v-model="name" required="required" placeholder="För- och efternamn">
                            </p>
                            <p>
                                <label for="email">Emailadress</label><br>
                                <input type="email" id="email" v-model="email" required="required" placeholder="E-mail">
                            </p>
                            <p>
                              Ange leveranspunkt:  
                            </p>
                            <section id="mapWrapper">
                                  <div id="map" v-on:click="setLocation">
                                    <div id="pos">
                                      <div v-bind:style="{ left: location.x + 'px', 
                                                           top: location.y + 'px' }">
                                          T
                                      </div>
                                    </div>
                                  </div>
                            </section>
                        <p>
                            <label for="pay">Betalningsmetod: </label><br>
                            <select id="pay" v-model="pay">
                                <option>Betalkort</option>
                                <option>Swish</option>
                                <option>Klarna</option>
                                <option>PayPal</option>
                                <option>Bitcoin</option>
                            </select>
                        </p>
                            <div>
                                Ange kön: <br/>
                                <label>
                                    <input type="radio" id="woman" v-model="gender" value="Kvinna">
                                    Kvinna
                                </label>
                            </div>
                            <div>
                                <label>
                                    <input type="radio" id="man" v-model="gender" value="Man">
                                    Man
                                </label>
                            </div>        
                            <div>
                                <label>
                                    <input type="radio" id="nonB" v-model="gender" value="Icke-binär">
                                    Icke-binär
                                </label>
                            </div>
                            <div>
                                <label>
                                    <input type="radio" id="nan" v-model="gender" value="Vill inte ange" checked>
                                    Vill inte ange
                                </label>
                            </div>
                        <br />
                        <button v-on:click="printForm" type="submit">
                            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTqqvxrsjyCLY9psJZjpWlRrPnz-hcar1L6QA&s" style="width: 35px">
                            Beställ burgare!
                        </button>
                      </form>
                    </section>
           
        </main>
            <hr>
        <footer>
            <p> &copy; 2024 Emma Grindefjord Lindskog</p>
        </footer>
 
       

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, image, kcal, meat, lactose) {
  this.name = name;
  this.url = image;
  this.kcal = kcal;
  this.meat = meat;
  this.lact = lactose;
};

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      name: '',
      email: '',
      pay: '',
      gender: '',
      orderedBurgers: {},
      location: { x: 0,
                  y: 0
                },
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function(event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location  = { x: event.clientX - 10 - offset.x,
                        y: event.clientY - 10 - offset.y };
    },              
    printForm: function(event) {
      let form = [this.name.trim(), this.email.trim(), this.gender.trim(), this.pay.trim()];
      console.log(form);
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y},
                                orderItems: this.orderedBurgers,
                                personalInfo: form
                              }
                 );
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log(this.orderedBurgers)
    },
  }
}
</script>

<style>
header {
    margin: 20px;
    height: 220px;
    overflow: hidden;
    border: 4px solid darksalmon
}
    header img {
        opacity:0.5;
        width: 100%;
        height:auto;
    }
    header h1 {
        position:absolute;
        margin-top: -700px;
        margin-left: 330px;
        font-size: 45px;
        font-family: Impact, sans-serif
    }
body {
    font-size: 2vh;
    font-family: Verdana, Helvetica, sans-serif;
    background-color:cornsilk;
 }
 #meny {
    background-color: indianred;
    color: white;
    border: 3px dashed white;
    padding: 15px
 }
 #information { 
    border: 3px dashed black;
    padding: 10px;
    background-color:white;
 }
 button {
    margin:20px;
    background-color: cornsilk;
    padding: 5px;
 }
    button:hover {
        color:pink;
        cursor: pointer;
    }
 section {
    margin:20px;
 }
 #mapWrapper {
  margin: 0px;
    overflow: scroll; 
    width: 700px;
    height: 450px; 
    border: 1px solid black;
 }
 .wrapper {
    display: grid;
    grid-gap: 15px;
    grid-template-columns: 440px 440px 440px;
    background-color: indianred;
    color: white;
}
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    
  }

  #pos {
    position: relative;
    margin: 0;
    padding: 0;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
  }
  
  #pos div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
</style>