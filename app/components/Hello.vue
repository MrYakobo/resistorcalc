<template>
  <div class="vertical-align has-text-centered">
    <h1 class="title is-1">Resistor calculator</h1>
    <div class="columns is-centered is-mobile is-gapless">
    <span class="column">Amount of bands:</span>
    <div class="column">
      <div class="select">
        <select v-model="amount">
          <option v-for="i in range(3,6)" :value="i">{{i+2}}</option>
        </select>
      </div>
    </div>
    <div class="column">
      <button @click="flip()" class="button is-light">Flip</button>
    </div>
    </div>
    <div class="columns is-centered is-mobile is-gapless">
    <div class="res">
    </div>
      <div class="column is-narrow " v-for="(cat,i) in categories" :key="cat">
      <div class="select">
        <select class="transition" :style="{background: bg(i), color: col(i)}" v-model="chosen[i]">
          <option disabled value=""></option>
          <option v-for="entry in entries" :selected="entry.value==1" :value="entry" v-if="colorCombination(i, entry)">{{entry.color}}</option>
        </select>
      </div>
        <span class="column">{{cat}}</span>
        <span>{{val(i)}}</span>
      </div>
    </div>
    <div class="subtitle is-1" v-html="total"></div>
    </div>
  </template>
<style>
  .transition {
    transition-duration: .1s;
  }
</style>

<script>
  export default {
    name: 'hello',
    data() {
      return {
        chosen: [],
        entries: [
          {color: 'black', value: 0, mul: 1e0, tol: null},
          {color: 'brown', value: 1, mul: 1e1, tol: 1},
          {color: 'red', value: 2, mul: 1e2, tol: 2},
          {color: 'orange', value: 3, mul: 1e3, tol: null},
          {color: 'yellow', value: 4, mul: 1e4, tol: null},
          {color: 'green', value: 5, mul: 1e5, tol: .5},
          {color: 'blue', value: 6, mul: 1e6, tol: .25},
          {color: 'violet', value: 7, mul: 1e7, tol: .10},
          {color: 'grey', value: 8, mul: null, tol: .05},
          {color: 'white', value: 9, mul: null, tol: null},
          {color: 'gold', value: null, mul: 1e-1, tol: 5},
          {color: 'silver', value: null, mul: 1e-2, tol: 10}
        ],
        amount: 3,
      }
    },
    watch: {
      amount(){
        //if amount changes, flush chosen array.
        this.chosen = []
      }
    },
    computed: {
      categories(){
        var str = ['st','nd','rd','th']
        var arr = []
        for(var i = 1; i <= this.amount; i++){
            arr.push(`${i}${str[i<str.length? i-1 : str.length-1]} band`)
        }
        arr.push('Multiplier', 'Tolerance')
        return arr
        // ['1st band','2nd band','3rd band','Multiplier','Tolerance']
      },
      total(){
        if(this.chosen.length === this.amount+2){
          var str = ""
          for(var i = 0; i < this.amount; i++){
              str += this.chosen[i].value
          }
          str = str.replace(/^0+/,'')
          console.log(str)
          if(str === '')
            str = 0
          var val = eval(str)
          val*=this.chosen[this.amount].mul
          var tol = this.chosen[this.amount+1].tol != null ? `&plusmn; ${this.chosen[this.amount+1].tol}%` : ''
          return `${this.prefix(val)}Ω ${tol}`
        }
      }
    },
    methods: {
      flip(){
          this.chosen = this.chosen.reverse()
      },
      val(i){
        if(this.chosen[i] == null) return ''
        if(i < this.amount){
          return this.chosen[i].value
        }
        if(i === this.amount) return this.chosen[i].mul
        if(i === this.amount+1) return this.chosen[i].tol
      },
      range(min,max){
        var arr = []
        for(var i = min; i < max; i++) arr.push(i)
        return arr
      },
      prefix(val){
        if(val === 0) return 0
          //helper method
          function between(min, max){
            return (val >= min && val < max)
          }
          var letters = ['p','n','µ','m','','K','M','G','T']
          for(var i = -12; i <= 12; i+=3){
            if(between(Math.pow(10,i), Math.pow(10, i+3))){
              var s = (val/Math.pow(10,i)) + letters[i/3+4]
              return s
            }
          }
      },
      colorCombination(i, entry){
        if(i < this.amount) return entry.value != null
        if(i == this.amount) return entry.mul != null
        if(i == this.amount+1) return entry.tol != null

        //shouldn't happen: 
        console.log(i)
      },
      bg(i){
        if(this.chosen[i] != null){
          return this.chosen[i].color
        }
        return 'none'
      },
      col(i){
        if(this.chosen[i] != null && !['white','yellow'].includes(this.chosen[i].color)){
          return '#fff'
        }
        return '#000'
      }
    }
  }

//object>svg>g>g>path
</script>
