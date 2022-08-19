<template>
  <el-dialog v-bind="$attrs" @open="onOpen" @close="onClose" title="生成代码">
    <template #footer>
      <el-button @click="close">取消</el-button>
      <el-button type="primary" @click="ok">确定</el-button>
    </template>
  </el-dialog>
</template>
<script>
export default {
  methods: {
    onOpen() {},
    onClose() {},
    close() {
      const fn = this.$attrs['onUpdate:modelValue'];
      fn && fn(false);
    },
    ok() {},
  },
};
</script>
