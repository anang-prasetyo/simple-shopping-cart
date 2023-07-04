<script setup>
import { onMounted, ref, watch } from "vue";

const myCart = ref([])
const addLocalStorage = () => {
  localStorage.setItem("cart", JSON.stringify(myCart.value));
}
const displayCalc = ref(null)
const toCalculate = ref(null)

const addToDisplayCalc = (n) => {
  const _addToDisplayCalc = (n) => {
    displayCalc.value ?
    displayCalc.value = String(displayCalc.value) + String(n) :
    displayCalc.value = n
  }
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
  }, 250)
}
const resetCalc = () => {
  displayCalc.value = null
  toCalculate.value = null
  readyToCalc.value = false
  operation.value = null
  myPromt.value = null
}
const myPromt = ref(null)
const subTotal = ref(0)
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
  <main>
    <section class="container d-flex flex-column justify-content-center align-items-center p-3 vh-100">
      <div class="">My Cart</div>
      <div v-if="myCart.length !== 0" class="text-center text-black-50">
        <ul class="list-unstyled">
          <li v-for="mc in myCart" :key="mc" class="d-flex gap-2">
            <div>{{ mc.name }}</div>
            <div>{{ mc.price + ' ' + mc.operation + ' ' + mc.qty + ' = ' + mc.total}}</div>
          </li>
        </ul>
      </div>
      <div v-else class="text-center text-black-50 p-2">Kosong</div>
      <!-- <button class="btn btn-dark" @click="addLocalStorage()">addLocalStorage</button> -->
      <div>
        <span v-if="myPromt">{{ myPromt }}</span>
        <span v-else style="display: block; min-height: 24px;"></span>
      </div>
      <input type="text" v-model="displayCalc" :placeholder="displayCalc ? '' : 'Type the price'" class="input-my">
      <div class="p-3">
        <div v-if="subTotal" class="fw-bold display-1">{{ subTotal }}</div>
        <span v-else style="display: block; min-height: 53.54px;"></span>
      </div>
      <div class="row w-100">
        <div class="col-12">
          <div class="row">
            <div class="col p-0">
              <button class="btn border border-1 button-grey btn-round" @click="resetCalc()">
                <span v-if="!displayCalc" class="fw-bold fs-2">AC</span>
                <span v-else class="fw-bold fs-2">C</span>
              </button>
            </div>
            <div class="col p-0">
              <button class="btn border border-1 button-grey btn-round fw-bold fs-1" @click="displayCalc = displayCalc.slice(0, -1)"><i class="bi bi-arrow-left-short"></i></button>
            </div>
            <div class="col p-0">
              <button class="btn border border-1 btn-round fw-bold fs-1" :class="readyToCalc && operation == '%' ? 'btn-dark' : 'button-grey'" @click="addToCalculate(), operation = '%', myPromt = 'Now type the discount'">%</button>
            </div>
            <div class="col p-0">
              <button class="btn border border-1 btn-round fw-bold fs-1" :class="readyToCalc && operation == '/' ? 'btn-dark' : 'button-warning'" @click="addToCalculate(), operation = '/'">&divide;</button>
            </div>
          </div>
        </div>
        <div class="col-9">
          <div class="row">
            <div v-for="n in 9" :key="n" class="col-4 d-flex flex-column justify-content-center align-items-center p-2">
              <button class="btn button-light border border-1 btn-round fw-bold fs-1" @click="addToDisplayCalc(n)">{{ n }}</button>
            </div>
            <div class="col-8 p-2">
              <button class="btn button-light border border-1 btn-pill fw-bold fs-1" @click="addToDisplayCalc(0)">0</button>
            </div>
            <div class="col-4 d-flex flex-column justify-content-center align-items-center p-2">
              <button class="btn button-light border border-1 btn-round fw-bold fs-1">,</button>
            </div>
          </div>
        </div>
        <div class="col-3 d-flex flex-column justify-content-center align-items-center row-gap-3">
          <button class="btn border border-1 btn-round fw-bold fs-1" :class="readyToCalc && operation == '*' ? 'btn-dark' : 'button-warning'" @click="addToCalculate(), operation = '*'">x</button>
          <button class="btn border border-1 btn-round fw-bold fs-1" :class="readyToCalc && operation == '-' ? 'btn-dark' : 'button-warning'" @click="addToCalculate(), operation = '-'">&minus;</button>
          <button class="btn border border-1 btn-round fw-bold fs-1" :class="readyToCalc && operation == '+' ? 'btn-dark' : 'button-warning'" @click="addToCalculate(), operation = '+'">+</button>
          <button class="btn border border-1 btn-round fw-bold fs-2 button-warning" @click="getResultCalc(operation)">Add</button>
        </div>
      </div>
      <!-- <div>
        <div>displayCalc : {{ displayCalc }}</div>
        <div>operation : {{ operation }}</div>
        <div>toCalculate : {{ toCalculate }}</div>
        <div>readyToCalc : {{ readyToCalc }}</div>
        <div>result : {{ result }}</div>
      </div> -->
    </section>

  </main>
</template>

<style lang="scss" scoped>
.input-my{
  outline: none;
  border: none;
  background: ghostwhite;
  padding: 1rem;
  text-align: center;
  border-radius: 3rem;
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
  height: var(--btn-size);
}
.btn-round{
  width: var(--btn-size);
}
.btn-pill{
  width: 100%;
}
</style>
