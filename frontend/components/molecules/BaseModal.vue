<template>
  <v-dialog v-model="dialog" width="800px">
    <!-- eslint-disable-next-line vue/no-unused-vars -->
    <template v-slot:activator="{ on }">
      <v-btn
        :class="classObject"
        :color="color"
        :outlined="isOutlined"
        :disabled="disabled"
        @click="dialog = true"
        class="mb-2 text-capitalize"
      >
        {{ text }}
      </v-btn>
    </template>
    <slot :close="close" />
  </v-dialog>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      default: '',
      required: true
    },
    disabled: {
      type: Boolean,
      default: false
    },
    isCreate: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      dialog: false
    }
  },

  computed: {
    classObject() {
      return this.isCreate ? [] : ['ml-2']
    },
    color() {
      return this.isCreate ? 'primary' : ''
    },
    isOutlined() {
      return !this.isCreate
    }
  },

  methods: {
    close() {
      this.dialog = false
    }
  }
}
</script>
