<template>
  <el-dialog
    :title="!dataForm.attractionsId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="scenicName">
      <el-input v-model="dataForm.scenicName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="scenicId">
      <el-input v-model="dataForm.scenicId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="scenicDescription">
      <el-input v-model="dataForm.scenicDescription" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="peopleNumber">
      <el-input v-model="dataForm.peopleNumber" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="status">
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
          attractionsId: 0,
          scenicName: '',
          scenicId: '',
          scenicDescription: '',
          peopleNumber: '',
          status: ''
        },
        dataRule: {
          scenicName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          scenicId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          scenicDescription: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          peopleNumber: [
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
        this.dataForm.attractionsId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.attractionsId) {
            this.$http({
              url: this.$http.adornUrl(`/attractions/info/${this.dataForm.attractionsId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.scenicName = data.attractions.scenicName
                this.dataForm.scenicId = data.attractions.scenicId
                this.dataForm.scenicDescription = data.attractions.scenicDescription
                this.dataForm.peopleNumber = data.attractions.peopleNumber
                this.dataForm.status = data.attractions.status
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
              url: this.$http.adornUrl(`/attractions/${!this.dataForm.attractionsId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'attractionsId': this.dataForm.attractionsId || undefined,
                'scenicName': this.dataForm.scenicName,
                'scenicId': this.dataForm.scenicId,
                'scenicDescription': this.dataForm.scenicDescription,
                'peopleNumber': this.dataForm.peopleNumber,
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
