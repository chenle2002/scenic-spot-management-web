<template>
  <el-dialog
    :title="!dataForm.scenicId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="景区名称" prop="scenicName">
      <el-input v-model="dataForm.scenicName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="景区描述" prop="scenicDescription">
      <el-input v-model="dataForm.scenicDescription" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="景区地址" prop="scenicAddress">
      <el-input v-model="dataForm.scenicAddress" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="开放状态（1为开放）" prop="status">
      <el-input v-model="dataForm.status" placeholder=""></el-input>
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
          scenicId: 0,
          scenicName: '',
          scenicDescription: '',
          scenicAddress: '',
          status: ''
        },
        dataRule: {
          scenicName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          scenicDescription: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          scenicAddress: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.scenicId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.scenicId) {
            this.$http({
              url: this.$http.adornUrl(`/scenicspot/info/${this.dataForm.scenicId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.scenicName = data.scenicSpot.scenicName
                this.dataForm.scenicDescription = data.scenicSpot.scenicDescription
                this.dataForm.scenicAddress = data.scenicSpot.scenicAddress
                this.dataForm.status = data.scenicSpot.status
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
              url: this.$http.adornUrl(`/scenicspot/${!this.dataForm.scenicId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'scenicId': this.dataForm.scenicId || undefined,
                'scenicName': this.dataForm.scenicName,
                'scenicDescription': this.dataForm.scenicDescription,
                'scenicAddress': this.dataForm.scenicAddress,
                'status': this.dataForm.status
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
