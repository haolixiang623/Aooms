<template>
  <div>
    <el-form
      :inline="true"
      size="mini"
      style="padding-bottom: 10px;margin-top: -8px;"
      class="demo"
    >

      <el-button type="primary" size="mini" icon="el-icon-plus" v-on:click="test()">新增</el-button>
      <el-button type="primary" size="mini" icon="el-icon-edit">编辑</el-button>
      <el-button type="danger" size="mini" icon="el-icon-delete">删除</el-button>

    </el-form>

    <el-table
      :data="currentTableData"
      v-loading="loading"
      size="mini"
      stripe
      style="width: 100%;"
      @selection-change="handleSelectionChange">

      <el-table-column
        type="selection"
        width="55">
      </el-table-column>

      <el-table-column label="卡密" :show-overflow-tooltip="true">
        <template slot-scope="scope">
          {{scope.row.key}}
        </template>
      </el-table-column>

      <el-table-column label="面值" width="60" align="center">
        <template slot-scope="scope">
          <el-tag
            size="mini"
            type="success">
            {{scope.row.value}}
          </el-tag>
        </template>
      </el-table-column>

      <el-table-column label="状态" width="50" align="center">
        <template slot-scope="scope">
          <boolean-control
            :value="scope.row.type"
            @change="(val) => {
              handleSwitchChange(val, scope.$index)
            }">
            <d2-icon
              name="check-circle"
              style="font-size: 20px; line-height: 32px; color: #67C23A;"
              slot="active"/>
            <d2-icon
              name="times-circle"
              style="font-size: 20px; line-height: 32px; color: #F56C6C;"
              slot="inactive"/>
          </boolean-control>
        </template>
      </el-table-column>

      <el-table-column label="状态" width="50" align="center">
        <template slot-scope="scope">
          <boolean-control-mini
            :value="scope.row.type"
            @change="(val) => {
              handleSwitchChange(val, scope.$index)
            }">
            <d2-icon
              name="check-circle"
              style="font-size: 20px; line-height: 32px; color: #67C23A;"
              slot="active"/>
            <d2-icon
              name="times-circle"
              style="font-size: 20px; line-height: 32px; color: #F56C6C;"
              slot="inactive"/>
          </boolean-control-mini>
        </template>
      </el-table-column>

      <el-table-column label="管理员" width="60">
        <template slot-scope="scope">
          {{scope.row.admin}}
        </template>
      </el-table-column>

      <el-table-column label="管理员备注" :show-overflow-tooltip="true">
        <template slot-scope="scope">
          {{scope.row.adminNote}}
        </template>
      </el-table-column>

      <el-table-column label="创建时间" width="150" :show-overflow-tooltip="true">
        <template slot-scope="scope">
          {{scope.row.dateTimeCreat}}
        </template>
      </el-table-column>

      <el-table-column label="使用状态" width="100" align="center">
        <template slot-scope="scope">
          <el-tag
            size="mini"
            :type="scope.row.used ? 'info' : ''">
            {{scope.row.used ? '已使用' : '未使用'}}
          </el-tag>
        </template>
      </el-table-column>

      <el-table-column label="使用时间" width="150" :show-overflow-tooltip="true">
        <template slot-scope="scope">
          {{scope.row.dateTimeUse}}
        </template>
      </el-table-column>

      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
                  size="mini"
                  @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button
                  size="mini"
                  type="danger"
                  @click="handleEdit(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>

    </el-table>
  </div>
</template>

<script>
import BooleanControl from '../BooleanControl'
import BooleanControlMini from '../BooleanControlMini'
import DataForm from '../DataForm.vue'

export default {
  components: {
    BooleanControl,
    BooleanControlMini,
    DataForm
  },
  props: {
    tableData: {
      default: () => []
    },
    loading: {
      default: false
    }
  },
  data () {
    return {
      currentTableData: [],
      multipleSelection: [],
      downloadColumns: [
        { label: '卡密', prop: 'key' },
        { label: '面值', prop: 'value' },
        { label: '状态', prop: 'type' },
        { label: '管理员', prop: 'admin' },
        { label: '管理员备注', prop: 'adminNote' },
        { label: '创建时间', prop: 'dateTimeCreat' },
        { label: '使用状态', prop: 'used' },
        { label: '使用时间', prop: 'dateTimeUse' }
      ]
    }
  },
  watch: {
    tableData: {
      handler (val) {
        this.currentTableData = val
      },
      immediate: true
    }
  },
  methods: {
    handleSwitchChange (val, index) {
      const oldValue = this.currentTableData[index]
      this.$set(this.currentTableData, index, {
        ...oldValue,
        type: val
      })
      // 注意 这里并没有把修改后的数据传递出去 如果需要的话请自行修改
    },
    handleSelectionChange (val) {
      this.multipleSelection = val
    },
    downloadDataTranslate (data) {
      return data.map(row => ({
        ...row,
        type: row.type ? '禁用' : '正常',
        used: row.used ? '已使用' : '未使用'
      }))
    },
    handleDownloadXlsx (data) {
      this.$export.excel({
        title: 'D2Admin 表格示例',
        columns: this.downloadColumns,
        data: this.downloadDataTranslate(data)
      })
        .then(() => {
          this.$message('导出表格成功')
        })
    },
    handleDownloadCsv (data) {
      this.$export.csv({
        title: 'D2Admin 表格示例',
        columns: this.downloadColumns,
        data: this.downloadDataTranslate(data)
      })
        .then(() => {
          this.$message('导出CSV成功')
        })
    },
    test () {
        alert(1);
    },
    handleEdit:function(index,row){

    }
  }
}
</script>
