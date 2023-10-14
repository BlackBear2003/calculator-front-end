<template>
  <div class="common-layout">
    <el-container id="root">
      <CommonHeader></CommonHeader>
      <div v-bind:style="{minHeight: Height+'px'}">
        <el-container style="padding: 30px 0px;">
        <el-aside>
        <el-menu
          default-active=""
          @select="selectFunction"
        >
          <el-menu-item index="1">
            <template #title>
              <el-icon><location /></el-icon>
              <span>计算器</span>
            </template>
          </el-menu-item>
          <el-sub-menu index="2">
            <template #title>
              <el-icon><location /></el-icon>
              <span>利率计算</span>
            </template>
            <el-menu-item index="2-1">
            <template #title>
              <el-icon><Tickets /></el-icon>
              <span>利率计算器</span>
            </template>
          </el-menu-item>
          <el-menu-item index="2-2">
            <template #title>
              <el-icon><Tools /></el-icon>
              <span>利率表</span>
            </template>
          </el-menu-item>
          </el-sub-menu>
        </el-menu>
        </el-aside>
        <el-main style="padding-left: 3%;">
          <el-row>
            <NormalCalculator v-if="ctrls.normalCal"></NormalCalculator>
          </el-row>
          <el-row v-if="ctrls.interestTable">
            <el-col :span="10">
              <el-row>
                <h1>存款利率表</h1>
              </el-row>
              <el-row>
                <DepositEditableTable></DepositEditableTable>
              </el-row>
            </el-col>
            <el-col :span="1"></el-col>
            <el-col :span="10">
              <el-row>
                <h1>贷款利率表</h1>
              </el-row>
              <el-row>
                <LoanEdiatableTable></LoanEdiatableTable>
              </el-row>
              
            </el-col>
          </el-row>
          <el-row>
            <InterestCalculator style="width: 350px;" v-if="ctrls.interestCal"></InterestCalculator>
          </el-row>
        </el-main>
        </el-container>
      </div>
      
      <el-footer>
        <CommonFooter style="height: 100px;"></CommonFooter>
      </el-footer>
    </el-container>
  </div>
</template>

<script setup>
import NormalCalculator from './functions/NormalCalculator.vue';
import CommonHeader from './components/CommonHeader.vue';
import CommonFooter from './components/CommonFooter.vue';
import DepositEditableTable from './functions/DepositEditableTable.vue';
import LoanEdiatableTable from './functions/LoanEdiatableTable.vue';
import InterestCalculator from './functions/InterestCalculator.vue';
import {
  Document,
  Menu as IconMenu,
  Location,
  Tickets,
  Tools,
  Setting,
} from '@element-plus/icons-vue'
import {onMounted, ref} from 'vue'

const ctrls = ref({
  normalCal: false,
  interestCal: false,
  interestTable: false
})

function showInterestCalculator() {
  ctrls.value.interestCal = true;
  ctrls.value.normalCal = false;
  ctrls.value.interestTable = false;
}

function showNormalCalculator() {
  ctrls.value.interestCal = false;
  ctrls.value.normalCal = true;
  ctrls.value.interestTable = false;
}

function showInterestTable() {
  ctrls.value.interestCal = false;
  ctrls.value.normalCal = false;
  ctrls.value.interestTable = true;
}
let Height = ref(0);

function selectFunction(index) {
  if (index === '1') {
    showNormalCalculator();
    console.log('点击了计算器');
  } else if (index === '2-1') {
    showInterestCalculator();
    console.log('点击了利率计算');
  } else if (index === '2-2') {
    showInterestTable();
    console.log('点击了利率计算');
  }
}

onMounted(() => {
  //动态设置内容高度 让footer始终居底   header+footer的高度是168
    Height.value = document.documentElement.clientHeight - 168;
    //监听浏览器窗口变化　
    window.onresize = ()=> {Height.value = document.documentElement.clientHeight - 168;}
})

</script>

<style>


</style>