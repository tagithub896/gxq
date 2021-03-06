<!-- 新增详情等的弹窗 -->
<template>
  <Modal
    v-model="showModal"
    title="新增"
    :mask-closable="false"
    width="600px">
    <Form ref="dealForm" :model="dealForm" :rules="validate" :label-width="96">
      <FormItem label="日志类别：" prop="logType" required>
        <Select v-model="dealForm.logType">
          <Option :value="item.code" v-for="item in logCode" :key="item.id">{{item.name}}</Option>
        </Select>
      </FormItem>
      <FormItem label="日志记录：" prop="logRecord" required>
        <Input
          type="textarea"
          :maxlength="200"
          placeholder="限制输入200个字符以内"
          v-model="dealForm.logRecord">
        </Input>
      </FormItem>
      <FormItem label="故障分类：" prop="faultClassification" required>
        <Select v-model="dealForm.faultClassification">
          <Option :value="item.code" v-for="item in faultClass" :key="item.id">{{item.name}}</Option>
        </Select>
      </FormItem>
      <FormItem label="故障级别：" prop="faultLevel" required>
        <Select v-model="dealForm.faultLevel">
          <Option :value="item.code" v-for="item in faultLevel" :key="item.id">{{item.name}}</Option>
        </Select>
      </FormItem>
      <FormItem label="附件：" prop="files" required>
        <hyUpload
          ref="fileUpload"
          action="/api/file/p/file/simple"
          :onSuccess="fileUploadSuccess"
          :format="['xls','xlsx','doc', 'docx','pdf']"
          :default-file-list="defaultFiles" />
      </FormItem>
      <FormItem label="处理时间：" prop="dealTime" required>
        <DatePicker
          v-model="dealForm.dealTime"
          format="yyyy-MM-dd"
          type="date"
          placeholder="请选择日期">
        </DatePicker>
      </FormItem>
    </Form>
    <div slot="footer">
      <Button class="modalBtn" type="default" @click="showModal = false" size="large">取消</Button>
      <Button class="modalBtn" type="primary" @click="submit" size="large">确定</Button>
    </div>
  </Modal>
</template>

<script>
import api from '@/api/axiosApi'
import operationApiList from '@/api/operationApiList'
// 文件上传
import hyUpload from '@/components/hengyun/hyUpload'
import { HANDLE_TYPES } from './../constant'

function getDealForm () {
  return {
    logType: '',
    logRecord: '',
    faultClassification: '',
    faultLevel: '',
    dealTime: '',
    files: []
  }
}

export default {
  // 分别为 日志类别，故障分类，故障级别
  props: ['logCode', 'faultClass', 'faultLevel'],
  components: {
    hyUpload
  },
  data () {
    const vm = this
    return {
      showModal: false,
      HANDLE_TYPES,
      handleType: '',
      dealForm: getDealForm(),
      defaultFiles: [],
      validate: {
        logType: [{validator: (rule, value, cb) => {
          if (!vm.dealForm.logType) {
            cb(new Error('不能为空'))
            return
          }
          cb()
        }}],
        logRecord: [{validator: (rule, value, cb) => {
          if (!vm.dealForm.logRecord) {
            cb(new Error('不能为空'))
            return
          }
          cb()
        }}],
        faultClassification: [{validator: (rule, value, cb) => {
          if (!vm.dealForm.faultClassification) {
            cb(new Error('不能为空'))
            return
          }
          cb()
        }}],
        faultLevel: [{validator: (rule, value, cb) => {
          if (!vm.dealForm.faultLevel) {
            cb(new Error('不能为空'))
            return
          }
          cb()
        }}],
        files: [{validator: (rule, value, cb) => {
          if (vm.dealForm.files.length === 0) {
            cb(new Error('文件不可为空'))
          } else {
            cb()
          }
        }}]
      }
    }
  },
  methods: {
    open (type, id) {
      const vm = this
      vm.dealForm = getDealForm()
      vm.handleType = type
      vm.$refs.dealForm.resetFields()
      vm.$refs.fileUpload.clearFiles()
      // 非新增
      if (type !== HANDLE_TYPES.CREATE) {
        vm.dealForm.id = id
        vm.getDetail()
      }
      vm.showModal = true
    },
    async getDetail () {
      const vm = this
      vm.showModal = true
      const res = await api(operationApiList.logGet, {
        id: vm.dealForm.id
      })
      if (res.data.errcode === 0) {
        const data = res.data.data
        vm.defaultFiles = data.files.map(item => {
          data.response = {
            data: {
              list: [{
                id: item.fileId,
                submittedFileName: item.fileName,
                size: item.fileSize,
                ext: item.fileType,
                url: item.fileUrl,
              }]
            }
          }
          return {
            name: item.fileName,
            url: item.fileUrl
          }
        })
        vm.dealForm = data
      } else {
        vm.$Message.info(res.data.errmsg)
      }
    },
    submit () {
      const vm = this
      if (vm.handleType === HANDLE_TYPES.DETAIL) {
        vm.showModal = false
        return
      }
      vm.$refs.dealForm.validate(valid => {
        if (valid) {
          const date = vm.dealForm.dealTime
          vm.doSave({
            ...vm.dealForm,
            dealTime: `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} 00:00:00`
          })
        } else {
          vm.$Message.info('表单验证失败')
        }
      })
    },
    async doSave (data) {
      const vm = this
      if (vm.handleType === HANDLE_TYPES.CREATE) {
        const res = await api(operationApiList.logSave, data)
        if (res.data && res.data.errcode === 0) {
          vm.$Message.success('保存成功！！！')
          vm.$emit('on-ok')
          vm.showModal = false
        } else {
          vm.$Message.error('保存失败！！！')
        }
      } else {
        const res = await api(operationApiList.logUpdate, data)
        if (res.data) {
          vm.$Message.success('更新成功！！！')
          vm.$emit('on-ok')
          vm.showModal = false
        }
      }
    },
    fileUploadSuccess(resp, file, fileList) {
      if (resp.errcode == 0) {
        this.dealForm.files = fileList.map(fileInfo => {
          const resp = fileInfo.response.data.list[0]
          return {
            fileId: resp.id,
            fileName: resp.submittedFileName,
            fileSize: resp.size,
            fileType: resp.ext,
            fileUrl: resp.url
          }
        })
        this.$refs.dealForm.validateField('files', res => {})
      } else {
        this.$Message.info(resp.errmsg)
      }
    }
  }
}
</script>
