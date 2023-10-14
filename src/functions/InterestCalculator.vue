<template>
<el-card class="box-card" >
    <el-space direction="vertical" >
    
    <el-form
        label-position="top"
        style="max-width: 100%"
        width="500px"
        label-width="350px"
    >
    <el-form-item label="">
        <el-radio-group v-model="functionName" label="label position">
        <el-radio-button label="deposit">贷款</el-radio-button>
        <el-radio-button label="loan">存款</el-radio-button>
    </el-radio-group>
    </el-form-item>
    <el-form-item label="金额" >
      <el-input v-model="money" />
    </el-form-item>
    <el-form-item label="时长(/年)">
      <el-input min-length="" v-model="period" />
    </el-form-item>
    <el-form-item label="">
        <el-button type="primary" @click="calculate()">计算</el-button>
    </el-form-item>
    <el-form-item label="">
        <el-text size="large">利息为：{{ result.toFixed(2) }} 元</el-text>
    </el-form-item>
    <el-form-item label="">
        <el-text size="large" width="400px">计算利率为： {{ recommend.interest }}%，时长描述为： {{ recommend.periodDescription }}</el-text>
    </el-form-item>
    </el-form>

  </el-space>
</el-card>
  
</template>


<script setup>
import { reactive, ref } from 'vue'
import axios from 'axios';
import {

} from '@element-plus/icons-vue'

const functionName = ref('deposit')
const result = ref(0.0)
const money = ref(0.0)
const period = ref(0.0)
const recommend = ref({
    interest: "0.0",
    period: 0.0,
    periodDescription: ''
})

async function calculate() {
    axios.get('http://81.68.212.127:8080/' + functionName.value + '/suitable?period=' + period.value)
    .then(response => {
          result.value = response.data.interest * money.value * period.value / 100.0;
          recommend.value = response.data;
        })
        .catch(error => {
          console.error('请求出错', error);
        });
}
</script>