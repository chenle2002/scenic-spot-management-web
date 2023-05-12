<template>
  <el-dialog
    :title="!dataForm.orderId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="visitorId">
      <el-input v-model="dataForm.visitorId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="scenicId">
      <el-input v-model="dataForm.scenicId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="orderDescription">
      <el-input v-model="dataForm.orderDescription" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="price">
      <el-input v-model="dataForm.price" placeholder=""></el-input>
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
          orderId: 0,
          visitorId: '',
          scenicId: '',
          orderDescription: '',
          price: '',
          createtime: ''
        },
        dataRule: {
          visitorId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          scenicId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          orderDescription: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          price: [
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
        this.dataForm.orderId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.orderId) {
            this.$http({
              url: this.$http.adornUrl(`//order/info/${this.dataForm.orderId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.visitorId = data.order.visitorId
                this.dataForm.scenicId = data.order.scenicId
                this.dataForm.orderDescription = data.order.orderDescription
                this.dataForm.price = data.order.price
                this.dataForm.createtime = data.order.createtime
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
              url: this.$http.adornUrl(`//order/${!this.dataForm.orderId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'orderId': this.dataForm.orderId || undefined,
                'visitorId': this.dataForm.visitorId,
                'scenicId': this.dataForm.scenicId,
                'orderDescription': this.dataForm.orderDescription,
                'price': this.dataForm.price,
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
