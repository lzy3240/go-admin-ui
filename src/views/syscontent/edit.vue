<template>
  <BasicLayout>
    <template #wrapper>
      <el-card class="box-card">
        <el-form ref="form" :model="form" label-width="80px" class="form-container">
          <el-form-item label="分类" prop="cateId">
            <el-select
              v-model="form.cateId"
              placeholder="请选择"
            >
              <el-option
                v-for="dict in cateIdOptions"
                :key="dict.key"
                :label="dict.value"
                :value="dict.key"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="名称" prop="name">
            <el-input
              v-model="form.name"
              placeholder="名称"
            />
          </el-form-item>
          <el-form-item label="状态" prop="status">
            <el-select
              v-model="form.status"
              placeholder="请选择"
            >
              <el-option
                v-for="dict in statusOptions"
                :key="dict.dictValue"
                :label="dict.dictLabel"
                :value="dict.dictValue"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="内容" prop="content">
            <!-- <el-input
                v-model="form.content"
                type="textarea"
                :rows="2"
                placeholder="请输入内容"
              /> -->
            <Tinymce ref="editor" v-model="form.content" :height="400" />
            <!-- <rict-text v-model="form.content" :height="400" /> -->
          </el-form-item>
          <el-form-item label="备注" prop="remark">
            <el-input
              v-model="form.remark"
              placeholder="备注"
            />
          </el-form-item>
          <el-form-item label="排序" prop="sort">
            <el-input
              v-model="form.sort"
              placeholder="排序"
            />
          </el-form-item>
          <el-form-item>
            <el-button v-loading="loading" style="margin-left: 10px;" type="success" @click="submitForm">
              保存
            </el-button>
            <el-button v-loading="loading" type="warning" @click="draftForm">
              Draft
            </el-button>
          </el-form-item>
        </el-form>
        <!-- <div slot="footer">
          <el-button type="primary" @click="submitForm">确 定</el-button>
          <el-button @click="cancel">取 消</el-button>
        </div> -->
      </el-card>
    </template>
  </BasicLayout>
</template>

<script>
import { getSysContent, updateSysContent } from '@/api/syscontent'
import { listSysCategory } from '@/api/syscategory'

// import FileChoose from '@/components/FileChoose'
import Tinymce from '@/components/Tinymce'
// import RictText from '@/components/richtext'
const defaultForm = {
  status: 'draft'
}
export default {
  name: 'CreateArticle',
  components: {
    // FileChoose,
    Tinymce
  },
  data() {
    return {
      loading: false,
      postForm: Object.assign({}, defaultForm),
      // 表单参数
      form: {},
      statusOptions: [],
      // 关系表类型
      cateIdOptions: [],
      // 表单校验
      rules: {
        cateId: [{ required: true, message: '分类id不能为空', trigger: 'blur' }],
        name: [{ required: true, message: '名称不能为空', trigger: 'blur' }],
        status: [{ required: true, message: '状态不能为空', trigger: 'blur' }]
      }
    }
  },
  created() {
    const id = this.$route.params && this.$route.params.id
    getSysContent(id).then(response => {
      this.form = response.data
    })
    this.getSysCategoryItems()
    this.getDicts('sys_content_status').then(response => {
      this.statusOptions = response.data
    })
  },
  methods: {
    // 关系
    getSysCategoryItems() {
      this.getItems(listSysCategory, undefined).then(res => {
        this.cateIdOptions = this.setItems(res, 'ID', 'name')
      })
    },
    draftForm() {

    },
    submitForm() {
      console.log(this.form)
      this.$refs['form'].validate(valid => {
        if (valid) {
          updateSysContent(this.form).then(response => {
            if (response.code === 200) {
              this.msgSuccess('修改成功')
              this.$store.dispatch('tagsView/delView', this.$route)
              this.$router.go(-1)
            } else {
              this.msgError(response.msg)
            }
          })
        }
      })
    }
  }
}
</script>
