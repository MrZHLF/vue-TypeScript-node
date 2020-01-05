<template>
  <div>
    <el-dialog title="编辑课程"
               :visible.sync="dialogVisible"
               :show-close="false"
               :close-on-click-modal="false">
      <el-form :rules="rules"
               :model="form"
               ref="ruleForm"
               label-width="100px"
               size="small"
               class="form-box">
        <el-form-item label="课程名称"
                      prop="title">
          <el-input v-model="form.title"
                    placeholder="请输入课程名称"></el-input>
        </el-form-item>

        <el-form-item label="课程等级"
                      prop="level">
          <el-select v-model="form.level"
                     placeholder="请选择课程等级">
            <el-option label="初级"
                       value="初级"></el-option>
            <el-option label="中级"
                       value="中级"></el-option>
            <el-option label="高级"
                       value="高级"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="报名人数"
                      prop="count">
          <el-input v-model="form.count"
                    placeholder="请输入报名人数"></el-input>
        </el-form-item>
        <el-form-item label="上线时间"
                      prop="date">
          <el-date-picker type="date"
                          v-model="form.date"
                          value-format="yyyy-mm-dd"
                          placeholder="请选择日期"></el-date-picker>
        </el-form-item>
        <el-form-item label="技术栈"
                      prop="type">
          <el-radio-group v-model="form.type">
            <el-radio label="vue"
                      name="type"></el-radio>
            <el-radio label="react"
                      name="type"></el-radio>
            <el-radio label="node"
                      name="type"></el-radio>
            <el-radio label="typescript"
                      name="type"></el-radio>
            <el-radio label="小程序"
                      name="type"></el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <span slot="footer"
            class="dialog-footer">
        <el-button size="small"
                   @click="$emit('closeDialog')">取消</el-button>
        <el-button type="primary"
                   size="small"
                   @click="addDialog('ruleForm')">确定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Provide, Prop } from "vue-property-decorator";
@Component({
  components: {}
})
export default class EditDialog extends Vue {
  // 属性或参数中使用 ！：表示强制解析（告诉typescript编译器，这里一定有值），常用于vue-decorator中的@Prop
  @Prop(Boolean) dialogVisible!: boolean;
  @Prop(Object) form!: {
    title: string;
    type: string;
    level: string;
    count: string;
    date: string;
    _id: string;
  };
  @Provide() rules: any = {
    title: [{ required: true, message: "请输入课程名称", trigger: "blur" }],
    level: [{ required: true, message: "请输入课程等级", trigger: "blur" }],
    count: [{ required: true, message: "请输入报名人数", trigger: "blur" }],
    date: [
      {
        type: "string",
        required: true,
        message: "请选择日期",
        trigger: "change"
      }
    ],
    type: [
      {
        required: true,
        message: "请选择技术栈",
        trigger: "change"
      }
    ]
  };
  addDialog(formName: any) {
    (this.$refs[formName] as any).validate((valid: boolean) => {
      if (valid) {
        (this as any).$axios
          .post(`/api/profiles/edit/${this.form._id}`)
          .then((res: any) => {
            console.log(res);
            this.$emit("closeDialog");
            this.$message({
              message: res.data.msg,
              type: "success"
            });
          });
      }
    });
  }
  created() {
    console.log(this.dialogVisible, this.form);
  }
}
</script>
<style lang="scss" scoped>
.form-box {
  .el-input,
  .el-select {
    width: 200px !important;
  }
}
</style>
