<template>
  <p>
    <el-input v-model="schemeUrl" placeholder="请输入openapi地址">
      <template #append>
        <el-button type="primary" @click="generator()">生成</el-button>
      </template>
    </el-input>
  </p>
  <el-tabs v-model="activeName" type="border-card">
    <el-tab-pane
      v-for="(item, i) in Object.keys(config)"
      :key="i"
      :label="item"
      :name="item"
    >
      <div class="tree-box" v-if="json">
        <tree-selector :data="json" />
      </div>
      <el-input
        type="textarea"
        :rows="10"
        placeholder="请输入模板"
        v-model="config[activeName].template"
      />
      <el-input
        type="textarea"
        :rows="10"
        placeholder="渲染结果"
        v-model="config[activeName].code"
      />
    </el-tab-pane>
  </el-tabs>
</template>

<script>
import axios from 'axios';
import Handlebars from 'handlebars';
import TreeSelector from './TreeSelector.vue';

Handlebars.registerHelper('loud', function (aString) {
  return aString.toUpperCase();
});

Handlebars.registerHelper('ispost', function (value) {
  return (value || '').toLowerCase() === 'post';
});

Handlebars.registerHelper('apiname', function (value) {
  const arr = (value || '').split('/');
  return (
    arr[arr.length - 2] +
    arr[arr.length - 1].replace(/^\S/, (s) => s.toUpperCase())
  );
});

Handlebars.registerPartial(
  'dataorparams',
  '{{#if (ispost @key)}}data{{else}}params{{/if}}'
);

export default {
  components: {
    TreeSelector,
  },
  data() {
    return {
      schemeUrl: 'http://127.0.0.1:4523/export/openapi/4?version=openapi30',
      json: null,
      activeName: 'API',
      config: {
        API: {
          template: `{{#each paths}}
{{#each this}}
// {{this.summary}}
export function {{apiname @../key}}({{> dataorparams}}) {
  return request({
    url: '{{@../key}}',
    method: '{{loud @key}}',
    {{> dataorparams}}\n
  })
}

{{/each}}{{/each}}`,
        },
        SearchForm: {
          template: `{{#each paths}}
{{#each this}}
// {{this.summary}}
  {{#each this.requestBody.content.[application/json].schema.properties}}
<form-item label="{{this.description}}" name="{{@key}}">
  <el-input placeholder="请输入{{this.description}}" clearable />
</form-item>
  {{/each}}
  {{#each this.parameters}}
<form-item label="{{this.description}}" name="{{this.name}}">
  <el-input placeholder="请输入{{this.description}}" clearable />
</form-item>
  {{/each}}

{{/each}}
{{/each}}`,
        },
        ProTable: {
          template: `{{#each paths}}
{{#each this}}
// {{this.summary}}
  {{#each this.responses.200.content.[application/json].schema.properties}}
<el-table-column prop="{{@key}}" label="{{this.description}}" width="120" />
  {{/each}}
{{/each}}

{{/each}}`,
        },
      },
    };
  },
  methods: {
    generator() {
      if (!this.schemeUrl) return;
      axios.get(this.schemeUrl).then((res) => {
        if (res.status === 200 && res.data) {
          this.json = res.data;
          Object.entries(this.config).forEach(([key, value]) => {
            const template = Handlebars.compile(value.template);
            this.config[key].code = template(res.data);
          });
        }
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.tree-box {
  max-height: 500px;
  overflow-y: auto;
  margin-bottom: 16px;
}
</style>
