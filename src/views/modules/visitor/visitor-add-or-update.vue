<template>
  <el-dialog
    :title="!dataForm.visitorId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="phone">
      <el-input v-model="dataForm.phone" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="visitorname">
      <el-input v-model="dataForm.visitorname" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="createtime">
      <el-input v-model="dataForm.createtime" placeholder=""></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          visitorId: 0,
          phone: '',
          visitorname: '',
          createtime: ''
        },
        dataRule: {
          phone: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          visitorname: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          createtime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.visitorId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.visitorId) {
            this.$http({
              url: this.$http.adornUrl(`/visitor/info/${this.dataForm.visitorId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.phone = data.visitor.phone
                this.dataForm.visitorname = data.visitor.visitorname
                this.dataForm.createtime = data.visitor.createtime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/visitor/${!this.dataForm.visitorId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'visitorId': this.dataForm.visitorId || undefined,
                'phone': this.dataForm.phone,
                'visitorname': this.dataForm.visitorname,
                'createtime': this.dataForm.createtime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
