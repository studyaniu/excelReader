<template>
  <!-- <input type="button" @click="handleExcelFile" value="显示"> -->
  <VueGoodTable :columns="columns" :rows="excelData" 
                :search-options="{ enabled: true }" 
                :sort-options="{ enabled: true }"
                :pagination-options="{ enabled: true, perPageDropdown: [10, 20, 50] }" />
</template>

<script>
// 使用axios来读取本地Excel文件
import axios from 'axios';
import * as XLSX from 'xlsx';
import 'vue-good-table-next/dist/vue-good-table-next.css'
import { VueGoodTable } from 'vue-good-table-next';

export default {
  name: 'App',
  data() {
    return {
      columns: [
        { label: 'ID', field: 'ID', type: 'String' },
        { label: 'baseMean', field: 'baseMean', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this baseMean',
          filterFn: this.columnFilterFn
        } },
        { label: 'log2FoldChange', field: 'log2FoldChange', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this log2FoldChange',
          filterFn: this.columnFilterFn
        }  },
        { label: 'lfcSE', field: 'lfcSE', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this lfcSE',
          filterFn: this.columnFilterFn
        }  },
        { label: 'stat', field: 'stat', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this stat',
          filterFn: this.columnFilterFn
        }  },
        { label: 'pvalue', field: 'pvalue', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this pvalue',
          filterFn: this.columnFilterFn
        }  },
        { label: 'padj', field: 'padj', filterOptions: {
          enabled: true,
          placeholder: 'Filter larger than this padj',
          filterFn: this.columnFilterFn
        }  },
      ],
      excelData: [],
    };
  },
  components: {
    VueGoodTable,
  },
  mounted: function(){
    this.handleExcelFile()
  },
  methods: {
    handleExcelFile() {
      // 读取Excel文件
      axios.get('/front-end-dynamic-table.xlsx', { responseType: 'arraybuffer' })
        .then((response) => {
          const data = response.data;
          const workbook = XLSX.read(data, { type: 'array' });
          const sheetName = workbook.SheetNames[0];
          const sheet = workbook.Sheets[sheetName];
          const datas = XLSX.utils.sheet_to_json(sheet);
          this.excelData = datas;
          // console.log('Excel data:', this.excelData);
        })
        .catch((error) => {
          console.error('Error reading Excel file:', error);
        });
    },
    columnFilterFn(data, filterString){
      if(filterString == 'NA'){
        if(data == 'NA') return data;
      }
      var x = parseInt(filterString)
      return data >= x;
    }
  },

}
</script>
