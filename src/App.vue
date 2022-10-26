<script setup>
import { ref } from 'vue';

const input = ref('')
const history = ref('')
const counterBrecet = ref(0)
const counterSqrBrecet = ref(0)

function clearInput() {
  input.value = ''
}

function addSymbol(symbol) {
  input.value = input.value + symbol
}

function addBrecet() {
  input.value = counterBrecet.value % 2 === 0 ? input.value + '(' : input.value + ')'
  counterBrecet.value++
}

function addSqrBrecet() {
  input.value = counterSqrBrecet.value % 2 === 0 ? input.value + '{' : input.value + '}'
  counterSqrBrecet.value++
}

function getResult() {
  try {
    input.value = input.value.replaceAll(/[,]/g, '.')
    input.value = input.value.replaceAll(/[A-Za-zА-Яа-яЁё]/g, '')
    history.value = input.value.split('').map((a) => a === '+' || a === '(' || a === ')' || a === '/' || a === '*' || a === '-' ? a = ' ' + a + ' ' : a = a).join('').replace('  ', ' ') + " "
    if (input.value.indexOf('{') > -1) {
      let fullString = input.value
      let save = fullString.slice(input.value.indexOf('{') + 1, input.value.indexOf('}'))
      save = Math.sqrt(getRes(save))
      input.value = fullString.replace(fullString.slice(input.value.indexOf('{'), input.value.indexOf('}') + 1), save)
    }
    let resultat = getRes(input.value)
    if (resultat === 0.09999999999999998) resultat = 0.1
    if (resultat === 0.19999999999999998) resultat = 0.2
    input.value = resultat === 0.30000000000000004 ? 0.3 : resultat
  } catch { input.value = '0' }
}

function getRes(string) {
  const stateNum = []
  const stateSymb = []
  const symbol = {
    "+": 1,
    "-": 1,
    "*": 2,
    "/": 2,
  }

  function parsingExpression(num, num2, symbol) {
    num = +num
    num2 = +num2
    let result = 0
    switch (symbol) {
      case '+':
        result = num + num2
        break
      case '-':
        result = num2 - num
        break
      case '*':
        result = num * num2
        break
      case '/':
        result = num2 / num
        break
    }
    return result
  }

  string = string.split('').map((a) => a === '+' || a === '(' || a === ')' || a === '/' || a === '*' || a === '-' ? a = ' ' + a + ' ' : a = a).join('').replace('  ', ' ').split(' ')
  string.forEach(a => {
    if (a) {
      if (Object.keys(symbol).includes(a) || a === "(" || a === ")") {
        if (a === ")") {
          while (stateSymb[stateSymb.length - 1] != "(") {
            stateNum.push(parsingExpression(stateNum.pop(), stateNum.pop(), stateSymb.pop()))
          }
          stateSymb.pop()
        } else if (stateSymb.length > 0 && symbol[a] < symbol[stateSymb[stateSymb.length - 1]]) {
          stateNum.push(parsingExpression(stateNum.pop(), stateNum.pop(), stateSymb.pop()))
          if (stateSymb.length > 0 && symbol[a] === symbol[stateSymb[stateSymb.length - 1]])
            stateNum.push(parsingExpression(stateNum.pop(), stateNum.pop(), stateSymb.pop()))
          stateSymb.push(a)
        } else {
          stateSymb.push(a)
        }
      } else {
        stateNum.push(a)
      }
    }
  })

  while (stateSymb.length != 0) {
    stateNum.push(parsingExpression(stateNum.pop(), stateNum.pop(), stateSymb.pop()))
  }
  return stateNum[0]
}
</script>

<template>
  <div class="border">
    <div class="main" @keyup.escape="clearInput">
      <div class="input__box">
        <input type="text" readonly v-model="history">
        <input type="text" v-model="input"
          @keyup="try { input = input.replace(/[A-Za-zА-Яа-яЁё]/, ''); if (input.length > 1 && input[0] === '0' && input[1] !== '.') input = input.slice(1) } catch { input = '0' };"
          @keyup.enter="getResult">
      </div>
      <hr>
      <div class="row__element">
        <button @click="clearInput">C</button>
        <button @click="addBrecet">()</button>
        <button @click="input = input / 100 > 0 ? input / 100 : 0">%</button>
        <button @click="addSymbol('/')">/</button>
      </div>
      <div class="row__element">
        <button @click="addSymbol('7')">7</button>
        <button @click="addSymbol('8')">8</button>
        <button @click="addSymbol('9')">9</button>
        <button @click="addSymbol('*')">*</button>
      </div>
      <div class="row__element">
        <button @click="addSymbol('4')">4</button>
        <button @click="addSymbol('5')">5</button>
        <button @click="addSymbol('6')">6</button>
        <button @click="addSymbol('-')">-</button>
      </div>
      <div class="row__element">
        <button @click="addSymbol('1')">1</button>
        <button @click="addSymbol('2')">2</button>
        <button @click="addSymbol('3')">3</button>
        <button @click="addSymbol('+')">+</button>
      </div>
      <div class="row__element">
        <button @click="addSymbol('0')">0</button>
        <button @click="addSqrBrecet">&#8730</button>
        <button @click="addSymbol('.')">.</button>
        <button id="result" @click="getResult" >=</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.border {
  padding: 38px 31px;
  max-width: 616px;
  max-height: 876px;
  border-radius: 18px;
  background: rgba(255, 255, 255, 0.2);
  /* opacity: 0.2; */
  z-index: 1;
}

hr {
  opacity: 0.35;
  width: 85%;
  margin-bottom: 37px;
}

.main {
  max-width: 554px;
  max-height: 800px;
  position: relative;
  border-radius: 18px;
  background: linear-gradient(155.23deg, #28518E 0%, #3A77D1 100%);
  box-shadow: 0px 82px 158px rgba(0, 0, 0, 0.35), 0px 24.7206px 47.6324px rgba(0, 0, 0, 0.228056), 0px 10.2677px 19.7841px rgba(0, 0, 0, 0.175), 0px 3.71362px 7.1555px rgba(0, 0, 0, 0.121944);
  padding: 5px;

}

.input__box {
  width: 100%;
  display: flex;
  align-items: center;
  flex-direction: column;
}

input:first-child {
  margin-top: 56px;
  font-size: 24px;
  padding-top: 50px;
}

input {
  color: white;
  border: none;
  outline: none;
  background: none;
  width: 90%;
  text-align: right;
  max-height: 76px;
  font-size: 56px;
}

.row__element {
  /* margin-top: 10px; */
  display: flex;
  justify-content: space-around;
}

button {
  align-items: center;
  color: white;
  background: none;
  width: 80px;
  height: 80px;
  font-size: 36px;
  cursor: pointer;
  margin-bottom: 16px;
  font-weight: 500px;
  line-height: 88px;
  border-radius: 50%;
  border: none;
  outline: none;
}

button:hover {
  background: rgba(255, 255, 255, 0.12)
}
#result{
  background-color: white; color:#3A77D1;
}
#result:hover{
  background: rgba(255, 255, 255, 0.75)
}
</style>
