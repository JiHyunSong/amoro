<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
/ -->

<script lang="ts">
import { message } from 'ant-design-vue'
import { computed, defineComponent, onMounted, reactive } from 'vue'
import { useRouter } from 'vue-router'
import loginService from '@/services/login.service'
import { usePlaceholder } from '@/hooks/usePlaceholder'
import useStore from '@/store'

interface FormState {
  username: string
  password: string
}

export default defineComponent({
  name: 'Login',
  setup() {
    const router = useRouter()
    const formState = reactive<FormState>({
      username: '',
      password: '',
    })
    const placeholder = reactive(usePlaceholder())
    const onFinish = async (values: FormState) => {
      try {
        const store = useStore()
        const res = await loginService.login({
          user: values.username,
          password: values.password,
        })
        if (res.code !== 200) {
          message.error(res.message)
          return
        }
        const { path, query } = store.historyPathInfo
        router.replace({
          path: path || '/',
          query,
        })
      }
      catch (error) {
        message.error((error as Error).message)
      }
    }

    const disabled = computed(() => {
      return !(formState.username && formState.password)
    })
    onMounted(() => {})
    return {
      placeholder,
      formState,
      onFinish,
      disabled,
    }
  },
})
</script>

<template>
  <div class="login-wrap g-flex-jc">
    <div class="login-content">
      <div class="img-logo">
        <img src="@/assets/images/logo-all1.svg" class="arctic-logo" alt="">
      </div>
      <div class="content-title">
        Lakehouse management system
      </div>
      <a-form
        :model="formState"
        name="normal_login"
        class="login-form label-120"
        @finish="onFinish"
      >
        <a-form-item
          label=""
          name="username"
          :rules="[{ required: true, message: placeholder.usernamePh }]"
        >
          <a-input
            v-model:value="formState.username"
            :placeholder="placeholder.usernamePh"
            style="height: 48px; background: #fff"
          >
            <template #prefix>
              <UserOutlined class="site-form-item-icon" />
            </template>
          </a-input>
        </a-form-item>
        <a-form-item
          label=""
          name="password"
          :rules="[{ required: true, message: placeholder.passwordPh }]"
        >
          <a-input-password
            v-model:value="formState.password"
            :placeholder="placeholder.passwordPh"
            style="height: 48px"
          >
            <template #prefix>
              <LockOutlined class="site-form-item-icon" />
            </template>
          </a-input-password>
        </a-form-item>
        <a-form-item>
          <a-button
            :disabled="disabled"
            type="primary"
            html-type="submit"
            class="login-form-button"
          >
            {{ $t("signIn") }}
          </a-button>
        </a-form-item>
      </a-form>
    </div>
  </div>
</template>

<style lang="less" scoped>
.login-wrap {
  width: 100vw;
  height: 100vh;
  margin: 0 auto;
  display: flex;
  align-items: center;
  background: linear-gradient(to top right, #0036A1 0%, #fff 100%);

  .login-content {
    width: 400px;
    border-radius: 16px;
    padding: 64px 60px;
    background: #ffffff;
    box-shadow: 0px 10px 24px 0px rgba(30, 31, 39, 0.08);
    .content-title {
      font-size: 16px;
      font-weight: 900;
      margin-bottom: 24px;
      color: #1E1F27;
    }
    :deep(.ant-input-prefix) {
      color: #0036a1;
      font-size: 20px;
      margin: 0px 14px 0px 4px;
    }
  }
  .img-logo {
    // margin: auto;
    margin-bottom: 24px;
    .arctic-logo {
      width: 120px;
    }
  }
  .desc {
    margin-top: 20px;
  }
  .login-form {
    .ant-form-item {
      margin-bottom: 16px;
    }
    :deep(.ant-input-affix-wrapper) {
      border-radius: 8px;
    }
    :deep(.ant-input) {
      color: #1E1F27;
    }
  }
  .login-form-button {
    margin-top: 36px;
    height: 48px;
    width: 100%;
    border-radius: 8px;
    color: #fff;
    border-color: transparent;
    &.ant-btn-primary {
      background: linear-gradient(to right, #2A72FF 0%, #0036A1 100%);
    }
    &.ant-btn-primary[disabled] {
      background: #cdcfd7;
    }
  }
}
</style>
