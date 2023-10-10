<script setup>
import { onMounted, ref, watch } from "vue";

const myCart = ref([])
// const addLocalStorage = () => {
//   localStorage.setItem("cart", JSON.stringify(myCart.value));
// }
const displayCalc = ref(null)
const toCalculate = ref(null)

const addToDisplayCalc = (n) => {
  const _addToDisplayCalc = (n) => {
    displayCalc.value ?
    displayCalc.value = String(displayCalc.value) + String(n) :
    displayCalc.value = n
  }
  const doCalc = (n) => {
    if (result.value){
      result.value = null
      resetCalc()
      if(readyToCalc.value){
        toCalculate.value == displayCalc.value ?
        [displayCalc.value = null,
        _addToDisplayCalc(n)] :
        _addToDisplayCalc(n)
      }
      else{
        _addToDisplayCalc(n)
      }
    }
    else{
      if(readyToCalc.value){
        toCalculate.value == displayCalc.value ?
        [displayCalc.value = null,
        _addToDisplayCalc(n)] :
        _addToDisplayCalc(n)
      }
      else{
        _addToDisplayCalc(n)
      }
    }
  }
  if (subTotal.value == 0){
    doCalc(n)
  }
  else{
    isSubTotal.value = false
    doCalc(n)
  }
}
const addToCalculate = () => {
  toCalculate.value = displayCalc.value
}
const readyToCalc = ref(false)
const operation = ref(null)
const result = ref(null)
const getResultCalc = (a) => {
  if (operation.value){
    if (a === '+'){
      result.value = Number(toCalculate.value) + Number(displayCalc.value) 
    }
    else if (a === '-'){
      result.value = Number(toCalculate.value) - Number(displayCalc.value) 
    }
    else if (a === '*'){
      result.value = Number(toCalculate.value) * Number(displayCalc.value) 
    }
    else if (a === '/'){
      result.value = Number(toCalculate.value) / Number(displayCalc.value) 
    }
    else if (a === '%'){
      result.value = Number(toCalculate.value) * (100 - Number(displayCalc.value))/100 
    }
    myCart.value.push({
      name: 'Item' + (myCart.value.length + 1),
      price: Number(toCalculate.value),
      qty: Number(displayCalc.value),
      operation: operation.value,
      total: result.value
    })
    toCalculate.value = null
    readyToCalc.value = false
    operation.value = null
  }
  else{
    result.value = Number(displayCalc.value)
    myCart.value.push({
      name: 'Item' + (myCart.value.length + 1),
      price: result.value,
      qty: 1,
      operation: '*',
      total: result.value
    })
  }
  setTimeout(() => {
    result.value = null
    displayCalc.value = null
    isSubTotal.value = true
  }, 250)
}
const resetCalc = () => {
  displayCalc.value = null
  toCalculate.value = null
  readyToCalc.value = false
  operation.value = null
  myPromt.value = null
}
const resetAllCalc = () => {
  subTotal.value = 0
  myCart.value = []
  resetCalc()
  isSubTotal.value = false
  tempTotal.value = []
}
const myPromt = ref(null)
const subTotal = ref(0)
const isSubTotal = ref(null)
const tempTotal = ref([])
const countTotal = () => {
  let lengthTempTotal = tempTotal.value.length
  tempTotal.value.push(myCart.value[lengthTempTotal].total)
  if (subTotal.value == 0){
    subTotal.value = tempTotal.value[0]
  }
  else {
    subTotal.value += tempTotal.value[lengthTempTotal];
  }
}
let iniAngka = 1500250123
const hasilAngka = ref(null)
const ubahAngka = () => {
  hasilAngka.value = String(iniAngka).split("")
  let pjgHasilAngka = (hasilAngka.value.length/3).toFixed(0)
  for (let a = 0; a<pjgHasilAngka; a++){
    if (a == 0){
      hasilAngka.value.splice(-3,0,',')
    }
    else{
      hasilAngka.value.splice(((-3)*(a+1))-a,0,',')
    }
  }
  hasilAngka.value = hasilAngka.value.join('')
}

const operations = ref(["÷", "x", "-", " +", "="])
watch(() => {
  if (toCalculate.value){
    readyToCalc.value = true
  }
  if (result.value){
    displayCalc.value = result.value
  }
  if(myCart.value.length !== 0 && result.value){
    countTotal()
  }
})
onMounted(() => {
  console.log('myCart start -> ', myCart.value);
})
</script>

<template>
  <main >
    <section class="container d-flex flex-column justify-content-center align-items-center p-3 s1 vh-100">
      <div v-if="myCart.length !== 0">
        <span v-if="myCart.length == 1">
          {{ myCart[myCart.length - 1].price + ' ' + myCart[myCart.length - 1].operation + ' ' + myCart[myCart.length - 1].qty }}
        </span>
        <span v-else>
          {{ (subTotal - tempTotal[tempTotal.length - 1]) + ' + (' + myCart[myCart.length - 1].price + ' ' + myCart[myCart.length - 1].operation + ' ' + myCart[myCart.length - 1].qty + ')' }}
        </span>
      </div>
      <div v-else style="display: block; min-height: 24px;"></div>
      <div class="p-3 w-100">
        <div v-if="isSubTotal" class="display-1 w-100 text-center">{{ subTotal }}</div>
        <input v-else style="display: block; min-height: 53.54px;" type="text" v-model="displayCalc" class="input-my-2 display-1 w-100">
      </div>
      <div class="d-flex flex-column align-items-center justify-content-end s2">
        <div class="row d-flex w-100 text-center gap-1 p-1 h-100">
          <button v-if="!displayCalc" @click="resetAllCalc()" class="col d-flex align-items-center justify-content-center button-grey rounded-circle btn fs-3">AC</button>
          <button v-else @click="resetCalc()" class="col d-flex align-items-center justify-content-center button-grey rounded-circle btn fs-3">C</button>
          <button class="col d-flex align-items-center justify-content-center button-grey rounded-circle btn fs-1 fw-bold" @click="displayCalc = displayCalc.slice(0, -1)"><i class="bi bi-arrow-left-short"></i></button>
          <button class="col d-flex align-items-center justify-content-center rounded-circle btn fs-3" @click="addToCalculate(), operation = '%', myPromt = 'Now type the discount'" :class="readyToCalc && operation == '%' ? 'btn-dark' : 'button-grey'">%</button>
          <button class="col d-flex align-items-center justify-content-center rounded-circle btn fs-1" :class="readyToCalc && operation == '/' ? 'btn -dark' : 'button-warning'" @click="addToCalculate(), operation = '/'">&divide;</button>
        </div>
        <div class="row d-flex w-100 text-center gap-1 p-1 h-100">
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(7)">7</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(8)">8</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(9)">9</button>
          <button class="col d-flex align-items-center justify-content-center rounded-circle btn fs-3 fs-3" @click="addToCalculate(), operation = '*'" :class="readyToCalc && operation == '*' ? 'btn-dark' : 'button-warning'">x</button>
        </div>
        <div class="row d-flex w-100 text-center gap-1 p-1 h-100">
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(4)">4</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(5)">5</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(6)">6</button>
          <button class="col d-flex align-items-center justify-content-center rounded-circle btn fs-3" @click="addToCalculate(), operation = '-'" :class="readyToCalc && operation == '-' ? 'btn-dark' : 'button-warning'">&minus;</button>
        </div>
        <div class="row d-flex w-100 text-center gap-1 p-1 h-100">
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(1)">1</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(2)">2</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-3" @click="addToDisplayCalc(3)">3</button>
          <button class="col d-flex align-items-center justify-content-center rounded-circle btn fs-3" @click="addToCalculate(), operation = '+'" :class="readyToCalc && operation == '+' ? 'btn-dark' : 'button-warning'">+</button>
        </div>
        <div class="row d-flex w-100 text-center gap-1 p-1 h-100">
          <button class="col-6 d-flex align-items-center justify-content-center button-light rounded-pill btn fs-3" @click="addToDisplayCalc(0)">0</button>
          <button class="col d-flex align-items-center justify-content-center button-light rounded-circle btn fs-2">,</button>
          <button class="col d-flex align-items-center justify-content-center button-warning rounded-circle btn fs-3" @click="getResultCalc(operation)">=</button>
        </div>
      </div>
    </section>

  </main>
</template>

<style lang="scss" scoped>
.s1{
  min-height: 100vh;
}
.input-my{
  outline: none;
  border: none;
  background: ghostwhite;
  padding: 1rem;
  text-align: center;
  border-radius: 3rem;
  &-2{
    outline: none;
    border: none;
    text-align: center;
  }
}
.button{
  &-grey{
    background: grey;
    color: white;
  }
  &-light{
    background: gainsboro;
  }
  &-warning{
    background: slateblue;
    color: white;
  }
}
.btn-round, .btn-pill{
  --btn-gap: 1rem;
  --btn-size: calc((100vw / 4) - var(--btn-gap));
  border-radius: var(--btn-size);
  height: 100%;
  // height: var(--btn-size);
}
.btn-round{
  width: 100%;
  // width: var(--btn-size);
}
.btn-pill{
  width: 100%;
}
.s2{
  width: 100%;
  height: 55vh;
}
@media (min-width: 576px) { 
  .s1{
    --max-w: 50vw;
    max-width: var(--max-w);
  }
  .s2{
    min-height: 73vh;
    width: 25vw;
  }
  // .btn-round, .btn-pill{
  //   --btn-gap: 1rem;
  //   --btn-size: calc((var(--max-w) / 4) - var(--btn-gap));
  // }
}
</style>
