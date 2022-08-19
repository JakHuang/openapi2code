<template>
  <div class="key-value-item">
    <span
      v-if="field"
      class="key"
      @click.prevent.stop="() => copyObj(field)"
      :title="field"
    >
      {{ field }}
    </span>
    <span
      :class="`value ${typeof value === 'string' ? '' : 'ellipsis'}`"
      @click.prevent.stop="() => copyObj(value)"
      :title="typeof value === 'string' ? value : JSON.stringify(value)"
    >
      {{ value }}
    </span>
  </div>
</template>

<script>
import { useClipboard } from '@vueuse/core';
import { ElMessage } from 'element-plus';

export default {
  props: ['field', 'value'],
  setup() {
    const { copy } = useClipboard();
    return {
      copy,
      copyObj(obj) {
        copy(typeof obj === 'string' ? obj : JSON.stringify(obj));
        ElMessage({
          message: '复制成功',
          type: 'success',
        });
      },
    };
  },
};
</script>

<style lang="scss" scoped>
.key-value-item {
  border-radius: 4px;
  height: 26px;
  overflow: hidden;
  .key,
  .value {
    display: inline-block;
    line-height: 24px;
    margin: 1px 0;
    padding: 0 5px;
    overflow: hidden;
    user-select: none;
    &:hover {
      background-color: #a5a5e4;
      color: #fff;
    }
    &:active {
      background-color: #7777e5;
      color: #fff;
    }
  }
  .key {
    background-color: #eee;
    color: #222;
  }
  .value {
    background-color: #cfe1ee;
    color: #656774;
    white-space: nowrap;
  }
  .ellipsis {
    max-width: 130px;
    text-overflow: ellipsis;
  }
}
</style>
