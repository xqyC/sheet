<template>
  <el-submenu :index="`${menu.path}`" v-if="menu.children && menu.children.length >= 1">
    <template #title>
      <i :class="menu.icon"></i>
      <span>{{ menu.name }}</span>
    </template>
    <MenuTree
      v-for="item in menu.children"
      :key="item.id"
      :menu="item"
    ></MenuTree>
  </el-submenu>
	<el-menu-item
    v-else
    :index="`${menu.path}`"
    @click="handleRoute(menu)"
  >
    <el-icon
      v-if="menu.meta?.icon"
      class="icons"
      :class="menu.meta?.icon"
    ></el-icon>
    <span>{{ menu.name }}</span>
  </el-menu-item>
</template>
<script lang="ts">
import { defineComponent } from "vue";
import { useRouter } from "vue-router";
export default defineComponent({
  name: "MenuTree",
	props: {
    menu: {
      type: Object,
      required: true,
    },
  },
	setup(props) {
    const router = useRouter();
    function handleRoute(menu: any) {
      router.push({ path: menu.path });
    }
    return {
      handleRoute,
    };
  },
});
</script>