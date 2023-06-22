<template>
  <el-card style="height: 70px;">
    <el-form :inline="true" class="form">
      <el-form-item label="用户名:">
        <el-input placeholder="请你输入搜索用户名"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" size="default">搜索</el-button>
        <el-button type="primary" size="default">重置</el-button>
      </el-form-item>
    </el-form>
  </el-card>
  <el-card style="margin: 10px 0;">
    <el-button type="primary" @click="addUser">添加用户</el-button>
    <el-button type="primary">批量删除</el-button>
    <!-- table展示用户信息 -->
    <el-table style="margin: 10px 0;" border :data="userArr">
      <el-table-column type="selection" align="center"></el-table-column>
      <el-table-column label="#" align="center" type="index"></el-table-column>
      <el-table-column label="ID" align="center" prop="id"></el-table-column>
      <el-table-column label="用户名字" align="center" prop="username" show-overflow-tooltip></el-table-column>
      <el-table-column label="用户名称" align="center" prop="name" show-overflow-tooltip></el-table-column>
      <el-table-column label="用户角色" align="center" prop="roleName" show-overflow-tooltip></el-table-column>
      <el-table-column label="创建时间" align="center" prop="createTime" show-overflow-tooltip></el-table-column>
      <el-table-column label="更新事件" align="center" prop="updateTime" show-overflow-tooltip></el-table-column>
      <el-table-column label="操作" width="300px" align="center">
        <template #="{ row, $index }">
          <el-button type="primary" size="small" icon="User">分类角色</el-button>
          <el-button type="primary" size="small" icon="Edit" @click="updateUser(row)">编辑</el-button>
          <el-button type="primary" size="small" icon="Delete">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页器 -->
    <el-pagination v-model:current-page="pageNo" v-model:page-size="pageSize" :page-sizes="[5, 7, 9, 11]"
      :background="true" layout="prev, pager, next, jumper, -> , sizes, total" :total="total" @current-change="getHasUser"
      @size-change="handler" />
  </el-card>
  <!-- 抽屉结构：完成添加新的用户账号|更新已有的账号信息 -->
  <el-drawer v-model="drawer">
    <template #header>
      <h4>添加用户</h4>
    </template>
    <template #default>
      <el-form>
        <el-form-item label="用户姓名">
          <el-input placeholder="请你输入用户的姓名" v-model="userParams.username"></el-input>
        </el-form-item>
        <el-form-item label="用户昵称">
          <el-input placeholder="请你输入用户的昵称" v-model="userParams.name"></el-input>
        </el-form-item>
        <el-form-item label="用户密码">
          <el-input placeholder="请你输入用户的密码" v-model="userParams.password"></el-input>
        </el-form-item>

      </el-form>
    </template>
    <template #footer>
      <div style="flex: auto">
        <el-button @click="cancel">取消</el-button>
        <el-button type="primary" @click="save">确定</el-button>
      </div>
    </template>
  </el-drawer>
</template>

<script setup lang="ts">
import { ref, onMounted, reactive } from 'vue'
import { reqUserInfo, reqAddOrUpdateUser } from '@/api/acl/user';
import type { UserResponseData, Records, User } from '@/api/acl/user/type'
import { ElMessage } from 'element-plus';
// 默认页码
let pageNo = ref<number>(1)
// 一页展示几条数据
let pageSize = ref<number>(5)
// 用户总个数
let total = ref<number>(0)
// 存储全部用户的数组
let userArr = ref<Records>([])
// 定义响应式数据控制抽屉的显示与隐藏
let drawer = ref<boolean>(false)
// 收集用户信息的响应式数据
let userParams = reactive({
  username: '',
  name: '',
  password: ''
})
// 组件挂载完毕
onMounted(() => {
  getHasUser()
})
// 获取全部已有的用户信息
const getHasUser = async (pager = 1) => {
  // 收集当前页码
  pageNo.value = pager
  let result: UserResponseData = await reqUserInfo(pageNo.value, pageSize.value)
  if (result.code == 200) {
    total.value = result.data.total
    userArr.value = result.data.records
  }
}
// 分页器下拉菜单的自定义事件的回调
const handler = () => {
  getHasUser()
}
// 添加用户按钮的回调
const addUser = () => {
  // 抽屉显示出来
  drawer.value = true
  Object.assign(userParams, {
    username: '',
    name: '',
    password: ''
  })
}
// 更新已有的用户按钮的回调
const updateUser = (row: User) => {
  drawer.value = true
}
// 保存按钮的回调
const save = async () => {
  let result: any = await reqAddOrUpdateUser(userParams)
  if (result.code == 200) {
    drawer.value = false
    ElMessage({ type: 'success', message: userParams.id ? '更新成功' : '添加成功' })
    getHasUser()
  } else {
    drawer.value = false
    ElMessage({ type: 'error', message: userParams.id ? '更新失败' : '添加失败' })
  }
}
// 取消按钮的回调
const cancel = () => {
  drawer.value = false
}
</script>

<style scoped>
.form {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>