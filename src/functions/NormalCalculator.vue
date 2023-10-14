<template>
    <div class="calculator">
      <div class="result" style="grid-area: result">
        {{ equation }}
      </div>
      <button style="grid-area: ac" @click="clear">AC</button>
      <button style="grid-area: ans" @click="getLastAnswer">ANS</button>
      <button style="grid-area: add" @click="append('+')">+</button>
      <button style="grid-area: subtract" @click="append('-')">-</button>
      <button style="grid-area: multiply" @click="append('×')">×</button>
      <button style="grid-area: divide" @click="append('÷')">÷</button>
      <button style="grid-area: equal" @click="calculate">=</button>
  
      <button style="grid-area: number-1" @click="append(1)">1</button>
      <button style="grid-area: number-2" @click="append(2)">2</button>
      <button style="grid-area: number-3" @click="append(3)">3</button>
      <button style="grid-area: number-4" @click="append(4)">4</button>
      <button style="grid-area: number-5" @click="append(5)">5</button>
      <button style="grid-area: number-6" @click="append(6)">6</button>
      <button style="grid-area: number-7" @click="append(7)">7</button>
      <button style="grid-area: number-8" @click="append(8)">8</button>
      <button style="grid-area: number-9" @click="append(9)">9</button>
      <button style="grid-area: number-0" @click="append(0)">0</button>
  
      <button style="grid-area: dot" @click="append('.')">.</button>
      
    </div>
  </template>
  
  <style scoped>
  
  .calculator {
    --button-width: 100px;
    --button-height: 100px;

    width: min-content;
    height: fit-content;
    
    display: grid;
    grid-template-areas: 
      "result result result result"
      "ac ac ans divide"
      "number-7 number-8 number-9 multiply"
      "number-4 number-5 number-6 subtract"
      "number-1 number-2 number-3 add"
      "number-0 number-0 dot equal";
    grid-template-columns: repeat(4, var(--button-width));
    grid-template-rows: repeat(6, var(--button-height));

    
    box-shadow: -8px -8px 16px -10px rgb(27, 150, 202), 8px 8px 16px -10px rgba(27, 150, 202);
    padding: 24px;
    border-radius: 20px;
  }
  
  .calculator button {
    margin: 8px;
    padding: 0;
    border: 0;
    display: block;
    outline: none;
    border-radius: calc(var(--button-height) / 2);
    font-size: 28px;
    font-family: Helvetica;
    font-weight: normal;
    color: #76daf6;
    background: linear-gradient(135deg, rgba(230, 230, 230, 1) 0%, rgba(246, 246, 246, 1) 100%);
    box-shadow: -4px -4px 10px -8px rgb(171, 222, 247), 4px 4px 10px -8px rgba(171, 222, 247);
  }
  
  .calculator button:active {
    box-shadow: -4px -4px 10px -8px rgba(255, 255, 255, 1) inset, 4px 4px 10px -8px rgba(0, 0, 0, .3) inset;
  }
  
  .result {
    text-align: right;
    line-height: var(--button-height);
    font-size: 48px;
    font-family: Helvetica;
    padding: 0 20px;
    color: #666;
    word-break: keep-all;
  }
  </style>
  
  <script>
    import axios from 'axios';

  export default{
    data(){
      return{
        equation: '0',
        isDecimalAdded: false,
        isOperatorAdded: false,
        isStarted: false,
        lastAnswerId: 0
      } 
    },
    methods: {
      // Check if the character is + / - / × / ÷
      isOperator(character) {
        return ['+', '-', '×', '÷'].indexOf(character) > -1
      },
      // When pressed Operators or Numbers
      append(character) {
        // Start
        if (this.equation === '0' && !this.isOperator(character)) {
          if (character === '.') {
            this.equation += '' + character
            this.isDecimalAdded = true
          } else {
            this.equation = '' + character
          }
          
          this.isStarted = true
          return
        }
        
        // If Number
        if (!this.isOperator(character)) {
          if (character === '.' && this.isDecimalAdded) {
            return
          }
          
          if (character === '.') {
            this.isDecimalAdded = true
            this.isOperatorAdded = true
          } else {
            this.isOperatorAdded = false
          }
          
          this.equation += '' + character
        }
        
        // Added Operator
        if (this.isOperator(character) && !this.isOperatorAdded) {
          this.equation += '' + character
          this.isDecimalAdded = false
          this.isOperatorAdded = true
        }
      },
      // When pressed '='
      calculate() {
        let origin_expression = this.equation;
        let expression = this.equation.replace(new RegExp('×', 'g'), '*').replace(new RegExp('÷', 'g'), '/')
        
        this.equation = parseFloat(eval(expression).toFixed(9)).toString()
        this.isDecimalAdded = false
        this.isOperatorAdded = false
        this.recordHistory(origin_expression, this.equation);
      },
      // When pressed 'AC'
      clear() {
        this.equation = '0'
        this.isDecimalAdded = false
        this.isOperatorAdded = false
        this.isStarted = false
      },
      getLastAnswer() {
        axios.get('http://81.68.212.127:8080/calculate?id='+this.lastAnswerId)
        .then(response => {
          this.append(response.data.result);
        })
        .catch(error => {
          console.error('请求出错', error);
        });
      },
      recordHistory(expression, result) {
        const formData = new FormData();

        formData.append('expression', expression);
        formData.append('result', result);
        axios.post('http://81.68.212.127:8080/calculate', formData)
        .then(response => {
          this.lastAnswerId = response.data.id;
        })
        .catch(error => {
          console.error('请求出错', error);
        });
      }
    }
  }
  </script>