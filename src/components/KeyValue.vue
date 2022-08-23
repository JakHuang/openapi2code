<template>
  <div class="key-value-item">
    <div
      v-if="field"
      class="key"
      @click.prevent.stop="() => copyObj(field)"
      :title="field"
    >
      {{ field }}
    </div>
    <div
      :class="`value ${typeof value === 'string' ? '' : 'ellipsis'} ${
        field ? '' : 'all-radius'
      }`"
      @click.prevent.stop="() => copyObj(value)"
      :title="typeof value === 'string' ? value : JSON.stringify(value)"
    >
      {{ value }}
    </div>
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
  height: 26px;
  overflow: hidden;
  flex-grow: 1;
  display: flex;
  margin-right: 10px;
  .key,
  .value {
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
    border-radius: 4px 0 0 4px;
    background-color: #eee;
    color: #222;
    flex-shrink: 0;
  }
  .value {
    border-radius: 0 4px 4px 0;
    background-color: #cfe1ee;
    color: #656774;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  .ellipsis {
    max-width: 130px;
    text-overflow: ellipsis;
  }
  .all-radius {
    border-radius: 4px;
  }
}
</style>
