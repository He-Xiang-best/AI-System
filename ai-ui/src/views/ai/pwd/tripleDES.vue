<template>
  <div>
    <div>
      <h1>在线TripleDES加密/解密</h1>
      <h2 class="alert alert-success" style="font-size: 20px">
        3DES又称Triple
        DES，是DES加密算法的一种模式，它使用2条不同的56位的密钥对数据进行三次加密。
        数据加密标准（DES）是美国的一种由来已久的加密标准，它使用对称密钥加密法，并于1981年被ANSI组织规范为ANSI
        X.3.92。
        DES使用56位密钥和密码块的方法，而在密码块的方法中，文本被分成64位大小的文本块然后再进行加密。比起最初的DES，3DES更为安全。
        3DES（即Triple
        DES）是DES向AES过渡的加密算法，它使用3条56位的密钥对数据进行三次加密。是DES的一个更安全的变形。
        它以DES为基本模块，通过组合分组方法设计出分组加密算法。比起最初的DES，3DES更为安全。
        Triple DES属于对称加密算法，加解密使用同一个秘钥。
      </h2>
    </div>
    <el-row :gutter="15">
      <el-form
        ref="elForm"
        :model="formData"
        :rules="rules"
        size="medium"
        label-width="100px"
      >
        <el-col :span="24">
          <el-form-item label="原文" prop="text">
            <el-input
              v-model="formData.text"
              type="textarea"
              placeholder="请输入原文"
              :autosize="{ minRows: 4, maxRows: 4 }"
              :style="{ width: '100%' }"
            ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="24">
          <el-form-item label="密匙" prop="keyWord">
            <el-input
              v-model="formData.keyWord"
              placeholder="可选"
              clearable
              :style="{ width: '100%' }"
            >
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="">
            <el-button
              type="warning"
              icon="el-icon-bottom"
              size="medium"
              round
              @click="encode()"
            >
              加密
            </el-button>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="">
            <el-button
              type="danger"
              icon="el-icon-top"
              size="medium"
              round
              @click="decode()"
            >
              解密
            </el-button>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="">
            <el-button
              type="info"
              icon="el-icon-refresh-right"
              size="medium"
              round
              @click="resetForm"
            >
              重置
            </el-button>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="">
            <el-button
              type="primary"
              icon="el-icon-refresh-right"
              size="medium"
              round
              @click="handleAdd"
            >
              保存
            </el-button>
          </el-form-item>
        </el-col>
        <el-col :span="24">
          <el-form-item label="密文" prop="output">
            <el-input
              v-model="formData.ciphertext"
              type="textarea"
              :autosize="{ minRows: 4, maxRows: 4 }"
              :style="{ width: '100%' }"
              placeholder="请输入密文"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-form>
    </el-row>

    <el-dialog :title="title" :visible.sync="open" width="500px" append-to-body>
      <el-form ref="form" :model="formData" :rules="rules" label-width="80px">
        <el-form-item label="原文" prop="text">
          <el-input
            v-model="formData.text"
            type="textarea"
            readonly=""
            :disabled="true"
            placeholder="请返回填写原文"
          />
        </el-form-item>
        <el-form-item label="key键" prop="keyWord">
          <el-input
            v-model="formData.keyWord"
            placeholder="可选"
            :disabled="true"
          />
        </el-form-item>
        <el-form-item label="密文" prop="ciphertext">
          <el-input
            v-model="formData.ciphertext"
            type="textarea"
            readonly=""
            :disabled="true"
            placeholder="请返回生成密文"
          />
        </el-form-item>
        <el-form-item label="备注" prop="remark">
          <el-input
            v-model="formData.remark"
            type="textarea"
            placeholder="请输入备注"
          />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button :loading="buttonLoading" type="primary" @click="submitForm"
          >确 定</el-button
        >
        <el-button @click="cancel">取 消</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import CryptoJS from "crypto-js";
import { listPwd, getPwd, delPwd, addPwd, updatePwd } from "@/api/ai/pwd";

export default {
  name: "TRIPLEDES",
  components: {},
  props: [],
  data() {
    return {
      formData: {
        id: undefined,
        text: "",
        keyWord: "",
        ciphertext: "",
        remark: undefined,
        encodeType: 'TripleDES',
        delFlag: undefined,
        createTime: undefined,
        createBy: undefined,
        updateTime: undefined,
        updateBy: undefined,
        userId: undefined,
      },
      title: "",
      open: false,
      buttonLoading: false,
      rules: {
        text: [
          {
            required: false,
            message: "请输入原文",
            trigger: "blur",
          },
        ],
        keyWord: [
          {
            required: false,
            message: "请输入密匙",
            trigger: "blur",
          },
        ],
        ciphertext: [{}],
      },
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
  methods: {
    encode() {
      if (this.formData.text === "") {
        this.resetForm();
        return;
      }
      this.formData.ciphertext = CryptoJS.TripleDES.encrypt(
        this.formData.text,
        this.formData.keyWord
      ).toString();
      if (this.formData.ciphertext == "") {
        this.$message.error("加密失败");
      } else this.$modal.msgSuccess("加密成功");
    },
    decode() {
      if (this.formData.ciphertext === "") {
        this.resetForm();
        return;
      }
      this.formData.text='';
      try{
 this.formData.text = CryptoJS.TripleDES.decrypt(
        this.formData.ciphertext,
        this.formData.keyWord
      ).toString(CryptoJS.enc.Utf8);
      if (this.formData.text == "") {
        this.$message.error("解密失败");
      } else this.$modal.msgSuccess("解密成功");
      }catch{
        this.$message.error("解密失败");
      }
    },
    handleAdd() {
      this.open = true;
      this.title = "保存密码";
    },
    submitForm() {
      this.buttonLoading = true;
      if (this.text == "") {
        this.$message.error("请输入原文");
        this.buttonLoading = false;
        return;
      }
      if (this.ciphertext == "") {
        this.$message.error("请输入密文");
        this.buttonLoading = false;
        return;
      }
      addPwd(this.formData)
        .then((response) => {
          this.$modal.msgSuccess("保存成功，可前往密码管理中心查看");
          this.open = false;
        })
        .finally(() => {
          this.buttonLoading = false;
        });
    },
    resetForm() {
      this.formData.text = "";
      this.formData.ciphertext = "";
      this.formData.keyWord = "";
    },
    cancel() {
      this.open = false;
    },
  },
};
</script>
<style>
h1 {
  text-align: center;
  font-size: 30px;
}
.alert-success {
  color: #3c763d;
  background-color: #dff0d8;
  border-color: #d6e9c6;
}
.alert {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  line-height: 40px;
  margin: 20px;
}
</style>

