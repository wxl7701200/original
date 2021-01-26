<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disablesubmit} }"
    @cancel="handleCancel"
    cancelText="关闭">
    <test-wxl-demo2-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit" normal></test-wxl-demo2-form>
  </j-modal>
</template>

<script>

  import TestWxlDemoForm from './TestWxlDemoForm'
  export default {
    name: "TestWxlDemoModal",
    components: {
      TestWxlDemoForm
    },
    data () {
      return {
        title:'',
        width:800,
        visible: false,
        disableSubmit: false
      }
    },
    methods: {
      add () {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.add();
        })
      },
      edit (record) {
        this.visible=true
        this.$nextTick(()=>{
          this.$refs.realForm.edit(record);
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.$refs.realForm.submitForm();
      },
      submitCallback(){
        this.$emit('ok');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      }
    }
  }
</script>