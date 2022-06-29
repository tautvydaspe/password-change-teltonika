<template>
  <div>
    <a-modal v-model="show" :title="'Change Password'" :footer="false" :closable="false" :keyboard="false" :maskClosable="false">
        <a-form-model :model="form" :rules="rules" ref="form">
    <a-form-model-item :label="'Password'" prop="password">
      <a-input-password v-model="form.password"/>
    </a-form-model-item>
    <a-form-model-item :label="'Confirmation'" prop="confirm">
      <a-input-password v-model="form.confirm"/>
    </a-form-model-item>
    <a-form-model-item>
      <a-button type="primary" @click="setPassword" style="">Change Password</a-button>
    </a-form-model-item>
  </a-form-model>
    </a-modal>
  </div>
</template>

<script>
export default {
  data () {
    const validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error(this.$t('admin.Please enter your password')))
      } else {
        if (this.form.confirm !== '') { this.$refs.form.validateField('confirm') }
        callback()
      }
    }

    const validatorConfirm = (rule, value, callback) => {
      if (value === '') {
        callback(new Error(this.$t('admin.Please enter your password again')))
      } else if (value !== this.form.password) {
        callback(new Error(this.$t('admin.Inconsistent input password twice!')))
      } else if (value.length > 30) {
        callback(new Error('Password is too long! It must be less than 30 characters.'))
      } else {
        callback()
      }
    }
    return {
      show: true,
      form: {
        password: '',
        confirm: ''
      },
      rules: {
        password: [{ validator: validatePass }],
        confirm: [{ validator: validatorConfirm }]
      }
    }
  },
  methods: {
    setPassword () {
      this.$refs.form.validate(valid => {
        if (!valid) return
        this.$rpc.call('ui', 'first_set', {
          lang: 'en',
          username: 'admin',
          password: this.form.password
        })
        this.show = false
      })
    }
  }
}
</script>

<style>
.vuci-login {
  width: 500px;
  top: 50%;
  left: 50%;
  position: fixed;
  transform: translate(-50%, -50%);
}

.footer {
  font-size: 20px;
  position: fixed;
  bottom: 10px;
  right: 20px;
  color: blue;
}
</style>
