<template>
  <el-dialog
    :visible.sync="visible"
    class="reward-dialog"
    title="现有积分/GameGem"
    :append-to-body="true"
    :modal-append-to-body="true"
    width="90%"
    top="20px"
  >
    <el-row class="content">
      <el-tabs v-model="activeName" type="card" @tab-click="clickHandler">
        <el-tab-pane label="In Process" name="one">
          <reward-list
            :data-list="listInProgress"
            :is-in-progress="true"
            v-on="$listeners"
          />
        </el-tab-pane>
        <el-tab-pane label="Completed" name="two">
          <reward-list :data-list="listCompleted" v-on="$listeners" />
        </el-tab-pane>
      </el-tabs>
    </el-row>
  </el-dialog>
</template>

<script>
import RewardList from '~components/offerList/RewardList'
import { getTask } from '@/api'
export default {
  name: 'RewardDialog',
  components: {
    RewardList
  },
  data() {
    return {
      visible: false,
      activeName: '',
      listInProgress: [],
      listCompleted: []
    }
  },
  methods: {
    init: function () {
      this.visible = true
      this.activeName = 'one'
      this.getList()
    },
    clickHandler: function (e) {
      this.activeName = e.name
    },
    getList: function () {
      const params = {
        playerId: window.sessionStorage.getItem('GameGemUID')
      }
      getTask(params).then(res => {
        if (res && res.code === 200) {
          // status=1 未完成；status=2 已完成
          // 修改为字段 taskStatus=1 未完成；taskStatus=2 已完成
          const ls = res.result.records
          this.listInProgress = ls.filter(item => {
            // return item.status === 1
            return item.taskStatus === 1
          })
          this.listCompleted = ls.filter(item => {
            // return item.status === 2
            return item.taskStatus === 2
          })
        } else {
          this.listInProgress = []
          this.listCompleted = []
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.reward-dialog {
  ::v-deep {
    .el-dialog__header {
      padding: 20px;
      color: #fff;
      background: #e1e1e1;
    }

    .el-dialog__body {
      padding: 0;
    }
  }
}
</style>
