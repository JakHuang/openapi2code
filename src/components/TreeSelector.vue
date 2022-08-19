<template>
  <div class="wrapper">
    <el-tree :data="dataTree" node-key="id" show-checkbox ref="treeRef">
      <template #default="{ node, data }">
        <div class="custom-tree-node">
          <key-value :field="data.key" :value="data.label" />
          <div>
            <a
              v-if="!node.isLeaf && !node.expanded"
              class="btn-text"
              @click.prevent.stop="() => expandedAll(node, data)"
            >
              递归展开
            </a>
            <a
              v-if="node.expanded"
              class="btn-text"
              @click.prevent.stop="() => foldAll(node, data)"
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

let nid = 1;

const buildTreeData = (key, value, res) => {
  if (isObject(value)) {
    const res2 = [];
    res.push({
      id: nid++,
      key,
      label: value,
      children: res2,
    });
    Object.entries(value).forEach(([key2, value2]) => {
      buildTreeData(key2, value2, res2);
    });
  } else if (isArray(value)) {
    const res2 = [];
    res.push({
      id: nid++,
      key,
      label: value,
      children: res2,
    });
    value.forEach((item) => {
      buildTreeData('', item, res2);
    });
  } else {
    res.push({
      id: nid++,
      key,
      label: value,
    });
  }
};

const recursivelySetExpanded = (node, val) => {
  node.expanded = val;
  if (Array.isArray(node.children)) {
    node.children.forEach((item) => {
      recursivelySetExpanded(item, val);
    });
  }
};

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
        buildTreeData('', item, res);
      });
      return res;
    },
  },
  methods: {
    expandedAll(node, data) {
      console.log(node);
      recursivelySetExpanded(node, true);
    },
    foldAll(node, data) {
      recursivelySetExpanded(node, false);
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
