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
              class="btn-text"
              @click.prevent.stop="() => generator(node, data)"
            >
              生成
            </a>
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
    <gen-code-dialog v-model="visible" />
  </div>
</template>

<script>
import { isArray, isObject } from '../utils/type.js';
import KeyValue from './KeyValue.vue';
import GenCodeDialog from './GenCodeDialog.vue';

export default {
  props: {
    data: {
      type: [Object, Array],
      default: () => [],
    },
  },
  components: {
    KeyValue,
    GenCodeDialog,
  },
  computed: {
    dataTree() {
      const res = [];
      this.buildTreeData('', this.data, res, []);
      return res;
    },
  },
  data() {
    return {
      nid: 1,
      visible: false,
    };
  },
  methods: {
    buildTreeData(key, value, res, pathArr) {
      const obj = {
        id: this.nid++,
        key,
        label: value,
        path: [...pathArr],
      };
      if (isObject(value)) {
        const res2 = [];
        obj.children = res2;
        res.push(obj);
        Object.entries(value).forEach(([key2, value2]) => {
          const paths = [...pathArr];
          paths.push(key2);
          this.buildTreeData(key2, value2, res2, paths);
        });
      } else if (isArray(value)) {
        const res2 = [];
        obj.children = res2;
        res.push(obj);
        value.forEach((item, i) => {
          const paths = [...pathArr];
          paths.push(i);
          this.buildTreeData('', item, res2, paths);
        });
      } else {
        res.push(obj);
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
    generator(node, data) {
      console.log(node, JSON.stringify(data.path));
      // this.visible = true;
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
