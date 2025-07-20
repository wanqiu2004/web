<template>
  <a-form
    :model="form"
    :rules="rules"
    ref="formRef"
    layout="vertical"
    class="register-form"
    @finish="onFinish"
  >
    <a-row :gutter="16">
      <a-col :xs="24" :sm="12">
        <a-form-item label="昵称" name="display_name">
          <a-input v-model:value="form.display_name" placeholder="可选昵称" />
        </a-form-item>
      </a-col>

      <a-col :xs="24" :sm="12">
        <a-form-item label="邮箱" name="email">
          <a-input v-model:value="form.email" placeholder="请输入邮箱地址" />
        </a-form-item>
      </a-col>



      <a-col :xs="24" :sm="12">
        <a-form-item label="密码" name="password">
          <a-input-password v-model:value="form.password" placeholder="请输入密码" />
        </a-form-item>
      </a-col>

      <a-col :xs="24" :sm="12">
        <a-form-item label="确认密码" name="confirm_password">
          <a-input-password v-model:value="form.confirm_password" placeholder="请再次输入密码" />
        </a-form-item>
      </a-col>

      <a-col :xs="24" :sm="12">
        <a-form-item label="图形验证码" name="captcha">
          <a-input-group compact>
            <a-input style="width: 60%" v-model:value="form.captcha" placeholder="请输入验证码" />
            <img
              :src="captchaUrl"
              @click="refreshCaptcha"
              style="width: 40%; height: 32px; cursor: pointer; border: 1px solid #d9d9d9; border-radius: 4px;"
              alt="captcha"
            />
          </a-input-group>
        </a-form-item>
      </a-col>
    </a-row>

    <a-form-item name="agree_terms">
      <a-checkbox v-model:checked="form.agree_terms">
        我已阅读并同意 <a href="#">《服务条款》</a>
      </a-checkbox>
    </a-form-item>

    <a-form-item>
      <a-button type="primary" html-type="submit" block :disabled="!form.agree_terms">
        注册
      </a-button>
    </a-form-item>
  </a-form>
</template>

<script setup>
import { reactive, ref } from 'vue'
import { message } from 'ant-design-vue'

const formRef = ref()

const form = reactive({
  username: '',
  email: '',
  display_name: '',
  password: '',
  confirm_password: '',
  captcha: '',
  agree_terms: false,
})

const captchaUrl = ref(getCaptchaUrl())

function getCaptchaUrl() {
  return `/api/captcha?ts=${Date.now()}`
}

function refreshCaptcha() {
  captchaUrl.value = getCaptchaUrl()
}

const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    {
      pattern: /^[a-zA-Z0-9_]{3,20}$/,
      message: '支持字母、数字、下划线，长度3-20位',
      trigger: 'blur',
    },
  ],
  email: [
    { required: true, message: '请输入邮箱地址', trigger: 'blur' },
    { type: 'email', message: '邮箱格式不正确', trigger: 'blur' },
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    {
      pattern: /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d@$!%*?&]{6,}$/,
      message: '密码需包含字母和数字，长度至少6位',
      trigger: 'blur',
    },
  ],
  confirm_password: [
    { required: true, message: '请确认密码', trigger: 'blur' },
    {
      validator(_, value) {
        if (value !== form.password) {
          return Promise.reject('两次密码不一致')
        }
        return Promise.resolve()
      },
      trigger: 'blur',
    },
  ],
  captcha: [
    { required: true, message: '请输入验证码', trigger: 'blur' },
  ],
  agree_terms: [
    {
      validator(_, value) {
        if (!value) return Promise.reject('必须同意服务条款')
        return Promise.resolve()
      },
    },
  ],
}

function onFinish() {
  message.success('注册成功！')
  console.log('注册信息：', { ...form })
}
</script>

<style scoped>
.register-form {
  max-width: 800px;
  margin: 0 auto;
  padding: 24px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}
</style>
