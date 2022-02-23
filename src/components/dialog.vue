<template>
  <el-dialog v-model="dialogData.ourterVisible" :title="dialogData.title">
    <template #default>
      <el-dialog
        v-if="dialogData.innertitle"
        v-model="dialogData.innerVisible"
        width="30%"
        title="Inner Dialog"
        append-to-body
      >
      </el-dialog>
    </template>
    <template #footer>
      <div class="dialog-footer" v-if="dialogData.footshow">
        <el-button
          v-for="(item, ind) in dialogData.footer"
          :key="ind"
          :size="item.size"
          :type="item.type"
          @click="item.click"
          >{{ item.title }}</el-button
        >
      </div>
    </template>
  </el-dialog>
</template>
<script lang="ts">
import { defineComponent, ref } from "vue";
export default defineComponent({
  props: {
    dialogData: {
      type: Object,
      required: true,
    },
  },
});
</script>