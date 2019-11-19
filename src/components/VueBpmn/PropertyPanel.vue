<template>
  <div class="property-panel" ref="propertyPanel">
    <el-form :inline="true" :model="form" label-width="100px">
      <el-form-item label="节点ID">
        <el-input v-model="form.id" disabled></el-input>
      </el-form-item>
      <el-form-item label="节点名称">
        <el-input v-model="form.name" @input="nameChange"></el-input>
      </el-form-item>
      <el-form-item label="节点颜色">
        <el-color-picker v-model="form.color" @active-change="colorChange"></el-color-picker>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  export default {
    name: 'PropertyPanel',
    props: {
      modeler: {
        type: Object,
        required: true
      }
    },
    data () {
      return {
        form: {
          id: '',
          name: '',
          color: null,
          nodeUser: ''
        },
        element: {}
      }
    },
    mounted () {
      this.handleModeler()
    },
    methods: {
      handleModeler () {
        // 监听节点选择变化
        this.modeler.on('selection.changed', (e) => {
          const element = e.newSelection[0]
          this.element = element
          if (!element) return
          this.form = {...element.businessObject, ...element.businessObject.$attrs}
        })

        //  监听节点属性变化
        this.modeler.on('element.changed', (e) => {
          const { element } = e
          if (!element) return
          //  新增节点需要更新回属性面板
          if (element.id === this.form.id) {
            this.form.name = element.businessObject.name
            this.form = {...this.form}
          }
        })
      },
      // 属性面板名称，更新回流程节点
      nameChange (name) {
        const modeling = this.modeler.get('modeling')
        modeling.updateLabel(this.element, name)
      },
      // 属性面板颜色，更新回流程节点
      colorChange (color) {
        const modeling = this.modeler.get('modeling')
        modeling.setColor(this.element, {
          fill: null,
          stroke: color
        })
        modeling.updateProperties(this.element, {color: color})
      }
    }
  }
</script>

<style lang="scss">
  .property-panel {
    position: absolute;
    right: 20px;
    top: 20px;
    border: 1px solid #cccccc;
    padding: 20px;
    border-radius: 5px;
    width: 300px;
  }
</style>
