<template>
  <form @submit.prevent="saveEmailConfig">
    <div class="grid grid-cols-1 gap-4 mb-4 md:grid-cols-2 md:mb-6">
      <sw-input-group
        :label="$t('wizard.mail.driver')"
        :error="driverError"
        required
      >
        <sw-select
          v-model="mailConfigData.mail_driver"
          :invalid="$v.mailConfigData.mail_driver.$error"
          :options="mailDrivers"
          :searchable="true"
          :allow-empty="false"
          :show-labels="false"
          @input="onChangeDriver"
        />
      </sw-input-group>

      <sw-input-group
        :label="$t('wizard.mail.host')"
        :error="hostError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.mail_host"
          :invalid="$v.mailConfigData.mail_host.$error"
          type="text"
          name="mail_host"
          @input="$v.mailConfigData.mail_host.$touch()"
        />
      </sw-input-group>
    </div>

    <div class="grid grid-cols-1 gap-4 mb-4 md:grid-cols-2 md:mb-6">
      <sw-input-group
        :label="$t('wizard.mail.port')"
        :error="portError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.mail_port"
          :invalid="$v.mailConfigData.mail_port.$error"
          type="text"
          name="mail_port"
          @input="$v.mailConfigData.mail_port.$touch()"
        />
      </sw-input-group>

      <sw-input-group
        :label="$t('wizard.mail.encryption')"
        :error="encryptionError"
        required
      >
        <sw-select
          v-model.trim="mailConfigData.mail_encryption"
          :invalid="$v.mailConfigData.mail_encryption.$error"
          :options="encryptions"
          :searchable="true"
          :show-labels="false"
          @input="$v.mailConfigData.mail_encryption.$touch()"
        />
      </sw-input-group>
    </div>

    <div class="grid grid-cols-1 gap-4 mb-4 md:grid-cols-2 md:mb-6">
      <sw-input-group
        :label="$t('wizard.mail.from_mail')"
        :error="fromEmailError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.from_mail"
          :invalid="$v.mailConfigData.from_mail.$error"
          type="text"
          name="from_mail"
          @input="$v.mailConfigData.from_mail.$touch()"
        />
      </sw-input-group>

      <sw-input-group
        :label="$t('wizard.mail.from_name')"
        :error="fromNameError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.from_name"
          :invalid="$v.mailConfigData.from_name.$error"
          type="text"
          name="name"
          @input="$v.mailConfigData.from_name.$touch()"
        />
      </sw-input-group>
    </div>
    <div class="grid grid-cols-1 gap-4 mb-6 md:grid-cols-2">
      <sw-input-group
        :label="$t('wizard.mail.ses_key')"
        :error="keyError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.mail_ses_key"
          :invalid="$v.mailConfigData.mail_ses_key.$error"
          type="text"
          name="mail_ses_key"
          @input="$v.mailConfigData.mail_ses_key.$touch()"
        />
      </sw-input-group>

      <sw-input-group
        :label="$t('wizard.mail.ses_secret')"
        :error="secretError"
        required
      >
        <sw-input
          v-model.trim="mailConfigData.mail_ses_secret"
          :invalid="$v.mailConfigData.mail_ses_secret.$error"
          :type="getInputType"
          name="mail_ses_secret"
          autocomplete="off"
          data-lpignore="true"
          @input="$v.mailConfigData.mail_ses_secret.$touch()"
        >
          <template v-slot:rightIcon>
            <eye-off-icon
              v-if="isShowPassword"
              class="w-5 h-5 mr-1 text-gray-500 cursor-pointer"
              @click="isShowPassword = !isShowPassword"
            />
            <eye-icon
              v-else
              class="w-5 h-5 mr-1 text-gray-500 cursor-pointer"
              @click="isShowPassword = !isShowPassword"
            />
          </template>
        </sw-input>
      </sw-input-group>
    </div>
    <sw-button
      :loading="loading"
      :disabled="loading"
      variant="primary"
      type="submit"
      class="mt-4"
    >
      <save-icon v-if="!loading" class="mr-2" />
      {{ $t('general.save') }}
    </sw-button>
  </form>
</template>

<script>
const { required, email, numeric } = require('vuelidate/lib/validators')
import { EyeIcon, EyeOffIcon } from '@vue-hero-icons/outline'

export default {
  components: {
    EyeIcon,
    EyeOffIcon,
  },
  props: {
    configData: {
      type: Object,
      require: true,
      default: Object,
    },
    loading: {
      type: Boolean,
      require: true,
      default: false,
    },
    mailDrivers: {
      type: Array,
      require: true,
      default: Array,
    },
  },
  data() {
    return {
      isShowPassword: false,
      mailConfigData: {
        mail_driver: '',
        mail_host: '',
        mail_port: null,
        mail_ses_key: '',
        mail_ses_secret: '',
        mail_encryption: 'tls',
        from_mail: '',
        from_name: '',
      },
      encryptions: ['tls', 'ssl', 'starttls'],
    }
  },
  validations: {
    mailConfigData: {
      mail_driver: {
        required,
      },
      mail_host: {
        required,
      },
      mail_port: {
        required,
        numeric,
      },
      mail_ses_key: {
        required,
      },
      mail_ses_secret: {
        required,
      },
      mail_encryption: {
        required,
      },
      from_mail: {
        required,
        email,
      },
      from_name: {
        required,
      },
    },
  },
  computed: {
    driverError() {
      if (!this.$v.mailConfigData.mail_driver.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_driver.required) {
        return this.$tc('validation.required')
      }
    },
    hostError() {
      if (!this.$v.mailConfigData.mail_host.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_host.required) {
        return this.$tc('validation.required')
      }
    },
    portError() {
      if (!this.$v.mailConfigData.mail_port.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_port.required) {
        return this.$tc('validation.required')
      }
      if (!this.$v.mailConfigData.mail_port.numeric) {
        return this.$tc('validation.numbers_only')
      }
    },
    encryptionError() {
      if (!this.$v.mailConfigData.mail_encryption.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_encryption.required) {
        return this.$tc('validation.required')
      }
    },
    fromEmailError() {
      if (!this.$v.mailConfigData.from_mail.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.from_mail.required) {
        return this.$tc('validation.required')
      }

      if (!this.$v.mailConfigData.from_mail.email) {
        return this.$tc('validation.email_incorrect')
      }
    },
    fromNameError() {
      if (!this.$v.mailConfigData.from_name.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.from_name.required) {
        return this.$tc('validation.required')
      }
    },
    keyError() {
      if (!this.$v.mailConfigData.mail_ses_key.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_ses_key.required) {
        return this.$tc('validation.required')
      }
    },
    secretError() {
      if (!this.$v.mailConfigData.mail_ses_secret.$error) {
        return ''
      }
      if (!this.$v.mailConfigData.mail_ses_secret.required) {
        return this.$tc('validation.required')
      }
    },
    getInputType() {
      if (this.isShowPassword) {
        return 'text'
      }
      return 'password'
    },
  },
  mounted() {
    for (const key in this.mailConfigData) {
      if (this.configData.hasOwnProperty(key)) {
        this.mailConfigData[key] = this.configData[key]
      }
    }
  },
  methods: {
    async saveEmailConfig() {
      this.$v.mailConfigData.$touch()
      if (!this.$v.mailConfigData.$invalid) {
        this.$emit('submit-data', this.mailConfigData)
      }

      return false
    },
    onChangeDriver() {
      this.$v.mailConfigData.mail_driver.$touch()
      this.$emit('on-change-driver', this.mailConfigData.mail_driver)
    },
  },
}
</script>
