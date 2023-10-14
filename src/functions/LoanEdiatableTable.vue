<template>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column label="时长描述" prop="periodDescription">
        <template #default="scope">
          <el-input v-if="scope.row.id === editingId" placeholder="输入时长描述" :value="scope.row.periodDescription"
            v-model="scope.row.periodDescription" />
        </template>
      </el-table-column>
      <el-table-column label="时长（/年）" prop="period">
        <template #default="scope">
          <el-input v-if="scope.row.id === editingId" placeholder="输入时长" :value="scope.row.period"
            v-model="scope.row.period" />
        </template>
      </el-table-column>
      <el-table-column label="利率" prop="interest">
        <template #default="scope">
          <el-input v-if="scope.row.id === editingId" placeholder="输入利率" :value="scope.row.interest"
            v-model="scope.row.interest" />
        </template>
      </el-table-column>/>
      <el-table-column text-align="right">
        <!-- <template #header>
          <el-input v-model="search" size="small" placeholder="输入描述" />
        </template> -->
        <template #default="scope">
          <el-button v-if="scope.row.id !== editingId" size="small" @click="handleEdit(scope.$index, scope.row)">
            ✏️
          </el-button>
          <el-button v-if="scope.row.id === editingId" size="small" @click="handleCheck(scope.$index, scope.row)">
            ✔️
          </el-button>
          <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">➖
          </el-button>
        </template>
      </el-table-column>
      
    </el-table>
    <el-button type="primary" class="mt-4" style="width: 100%" @click="onAddDeposit()">Add</el-button>
  </template>
    
  <script>
  import { computed, reactive, ref, onMounted, onBeforeMount} from 'vue'
  import axios from 'axios';
  
  export default{
    data() {
      return {
        editingId: null,
        search: '',
        tableData: []
      }
    },
    methods: {
      async refershTableData() {
        try {
          const response = await axios.get('http://81.68.212.127:8080/loan');
          this.tableData = response.data;
          console.log(this.tableData);
          return response.data;
        } catch (error) {
          console.error('请求出错', error);
        }
      },
      handleEdit(index, row) {
        this.editingId = row.id;
      },
      async handleDelete(index, row) {
        
        try {
          const confirmDelete = window.confirm("确认删除这行记录吗？");
          if (confirmDelete) {
            
            await axios.delete('http://81.68.212.127:8080/loan?id='+row.id);
            this.editingId = null;
            this.refershTableData();
          }
        } catch (error) {
          console.error('请求出错', error);
        }
      },
      async handleCheck(index, row) {
        try {
          // judge if the row is new
          if(row.id === 0) {
            const formData = new FormData();
            formData.append("periodDescription", row.periodDescription);
            formData.append("period", row.period);
            formData.append("interest", row.interest);
            await axios.post('http://81.68.212.127:8080/loan', formData);
          } else {
            await axios.put('http://81.68.212.127:8080/loan', row);
          }
          this.editingId = null;
          this.refershTableData();
        } catch (error) {
          console.error('请求出错', error);
        }
      },
      onAddDeposit() {
        this.tableData.push(
          {
            "id": 0,
            "periodDescription": "",
            "period": 0,
            "interest": "0"
          }
        )
        this.editingId = 0;
      }
    },
    mounted: function () {
    this.$nextTick(function () {
      this.refershTableData();
    })
  }
  }
 
  </script>
    