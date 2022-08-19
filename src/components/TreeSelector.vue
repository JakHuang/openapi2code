<template>
  <div class="wrapper">
    <el-tree
      :data="dataTree"
      :default-expanded-keys="[1]"
      node-key="id"
      show-checkbox
      ref="treeRef"
    >
      <template #default="{ node, data }">
        <div class="custom-tree-node">
          <key-value :field="data.key" :value="data.label" />
          <div>
            <a
              v-if="!node.isLeaf && !node.expanded"
              class="btn-text"
              @click.prevent.stop="() => recursivelySetExpanded(node, true)"
            >
              递归展开
            </a>
            <a
              v-if="!node.isLeaf && node.expanded"
              class="btn-text"
              @click.prevent.stop="() => recursivelySetExpanded(node, false)"
            >
              递归折叠
            </a>
          </div>
        </div>
      </template>
    </el-tree>
  </div>
</template>

<script>
import { isArray, isObject } from '../utils/type.js';
import KeyValue from './KeyValue.vue';

export default {
  props: {
    data: {
      type: [Object, Array],
      default: () => [],
    },
  },
  components: {
    KeyValue,
  },
  computed: {
    dataTree() {
      const res = [];
      let arr;
      if (isObject(this.data)) {
        arr = [this.data];
      } else if (isArray(this.data)) {
        arr = this.data;
      } else {
        throw new Error('数据类型错误');
      }
      arr.forEach((item) => {
        this.buildTreeData('', item, res);
      });
      return res;
    },
  },
  data() {
    return {
      nid: 1,
    };
  },
  methods: {
    buildTreeData(key, value, res) {
      if (isObject(value)) {
        const res2 = [];
        res.push({
          id: this.nid++,
          key,
          label: value,
          children: res2,
        });
        Object.entries(value).forEach(([key2, value2]) => {
          this.buildTreeData(key2, value2, res2);
        });
      } else if (isArray(value)) {
        const res2 = [];
        res.push({
          id: this.nid++,
          key,
          label: value,
          children: res2,
        });
        value.forEach((item) => {
          this.buildTreeData('', item, res2);
        });
      } else {
        res.push({
          id: this.nid++,
          key,
          label: value,
        });
      }
    },
    recursivelySetExpanded(node, val) {
      node.expanded = val;
      if (Array.isArray(node.childNodes)) {
        node.childNodes.forEach((item) => {
          this.recursivelySetExpanded(item, val);
        });
      }
    },
  },
};
</script>

<style scoped lang="scss">
.wrapper {
  padding: 4px;
}
.custom-tree-node {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 14px;
  padding-right: 8px;
}
.btn-text {
  color: #0088fa;
}
</style>

<style lang="scss">
.wrapper {
  .el-tree-node__content {
    height: auto !important;
  }
}
</style>
