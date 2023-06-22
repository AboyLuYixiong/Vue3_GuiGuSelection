<template>
  <div class="logo" v-if="setting.logoHidden" :class="{ fold: LayoutSettingStore.fold ? true : false }">
    <img :src="setting.logo" alt="">
    <p>{{ setting.title }}</p>
  </div>
</template>

<script setup lang="ts">
// 引入设置标题与logo的配置文件
import setting from '@/setting'
import useLayOutSettingStore from '@/store/modules/setting';
let LayoutSettingStore = useLayOutSettingStore();

</script>
<script lang="ts">
export default {
  name: 'Logo'
}
</script>

<style scoped lang="scss">
.logo {
  width: 100%;
  height: $base-menu-logo-height;
  color: #fff;
  display: flex;
  align-items: center;
  padding: 20px;
  &.fold {
    img {
      margin-left: -15px;
    }
  }

  img {
    width: 40px;
    height: 40px;
  }

  p {
    font-size: $base-logo-title-fontSize;
    margin-left: 10px;
  }
}
</style>