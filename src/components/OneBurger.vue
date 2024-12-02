<template>

    <div class="box">
        <h3>{{ burger.name }}</h3>
            <img v-bind:src="burger.url" alt="Span" title={{burger.name}} style="width: 300px">
            <ul>
                <li v-if="burger.meat">Innehåller <span class="ingredient">kött</span></li>
                <li v-if="burger.lact">Innehåller <span class="ingredient">mejeriprodukter</span></li>
                <li>{{ burger.kcal }} kcal </li>
            </ul>
            <button v-on:click= "removeBurger" v-bind:disabled="burger.amountOrdered <= 0"> - </button>
            <span>
              Antal: {{ burger.amountOrdered }}
            </span>
            <button v-on:click= "addBurger" > + </button>
    </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  methods: {
    addBurger: function () {
      this.burger.amountOrdered += 1;
      this.changeOrder();
    },
    removeBurger: function () {
      this.burger.amountOrdered -= 1;
      this.changeOrder();
    },
    changeOrder: function () {
        console.log('Emitting order:', this.burger.name, this.burger.amountOrdered);
        this.$emit('orderedBurger', { name:   this.burger.name,
                                      amount: this.burger.amountOrdered
                                    }
                  );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .ingredient {
    font-weight: bold;
    }

  .box {
    background-color: white;
    color: black;
    border-radius: 5px;
    padding: 20px;
    font-size: 120%;
  }

  button {
  padding: 8px 15px;
  font-size: 20px;
  cursor: pointer;
}

</style>