<!--  eslint-disable vue/valid-template-root -->
<template>
  <v-menu v-if="label" v-model="showMenu" offset-y>
    <template v-slot:activator="{ on }">
      <span :style="{ boderColor: color }" v-on="on" class="highlight bottom">
        <span
          class="highlight__content"
        >{{ content
        }}<v-icon
          @click.stop="remove"
          class="delete"
        >mdi-close-circle</v-icon></span><span
          :data-label="label"
          :style="{ backgroundColor: color, color: textColor }"
          class="highlight__label"
        />
      </span>
    </template>
    <v-list dense min-width="150" max-height="400" class="overflow-y-auto">
      <v-list-item
        v-for="(item, i) in relationLabels"
        :key="i"
        v-shortkey.once="[item.suffix_key]"
        @shortkey="update(item)"
        @mouseenter="showCascade(item, i)"
      >
        <v-menu v-model="showCascadeMenu[i]" :offset-x="true">
          <!-- eslint-disable-next-line vue/no-unused-vars -->
          <template v-slot:activator="{ on }">
            <v-list-item-title slot="activator" v-text="item.text" />
          </template>
          <v-list
            dense
            min-width="150"
            max-height="400"
            class="overflow-y-auto"
          >
            <v-list-item
              v-for="(obj, obj_index) in objects"
              :key="obj_index"
              @click="makeRelation(obj, item)"
            >
              <v-list-item-content>
                <v-list-item-title v-text="obj.text" />
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-list-item-content>
          <v-list-item-title v-text="item.text" />
          <v-list-item-title />
        </v-list-item-content>
        <v-list-item-icon>
          <v-icon>mdi-chevron-right</v-icon>
        </v-list-item-icon>
      </v-list-item>
    </v-list>
    <!-- <v-menu
      v-model="showCascadeMenu"
      :position-x="x"
      :position-y="y"
      absolute
      offset-y
    >
      <v-list dense min-width="150" max-height="400" class="overflow-y-auto">
        <v-list-item
          v-for="(item, i) in objects"
          :key="i"
          @click="makeSpo(item)"
        >
          <v-list-item-content>
            <v-list-item-title v-text="item.text" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-menu> -->
  </v-menu>
  <!-- eslint-disable-next-line vue/valid-template-root -->
  <span v-else>{{ content }}</span>
</template>

<script>
import Vue from 'vue'
import { idealColor } from '~/plugins/utils.js'
Vue.use(require('vue-shortkey'))
export default {
  props: {
    content: {
      type: String,
      default: '',
      required: true
    },
    label: {
      type: String,
      default: ''
    },
    color: { type: String, default: '#64FFDA' },
    labels: {
      type: Array,
      default: () => [],
      required: true
    },
    relationLabels: {
      type: Array,
      default: () => [],
      required: true
    },
    objects: {
      type: Array,
      default: () => [],
      required: true
    }
  },
  data() {
    return {
      showMenu: false,
      showCascadeMenu: [],
      x: 0,
      y: 0
    }
  },
  computed: {
    textColor() {
      return idealColor(this.color)
    }
  },
  created() {
    this.showCascadeMenu = [...Array(this.relationLabels.length)].map(_ => false)
  },
  methods: {
    update(label) {
      this.$emit('update', label)
      this.showMenu = false
    },
    remove() {
      this.$emit('remove')
    },
    showCascade(item, i) {
      console.log(this.objects)
      this.showCascadeMenu = [...Array(this.relationLabels.length)].map(_ => false)
      this.showCascadeMenu[i] = true
    },
    makeRelation(obj, relation) {
      this.$emit('makeRelation', { obj, relation })
      this.showCascadeMenu = [...Array(this.relationLabels.length)].map(_ => false)
    }
  }
}
</script>

<style scoped>
.highlight.blue {
  background: #edf4fa !important;
}
.highlight.bottom {
  display: block;
  white-space: normal;
}
.highlight:first-child {
  margin-left: 0;
}
.highlight {
  border: 2px solid;
  margin: 4px 6px 4px 3px;
  vertical-align: middle;
  box-shadow: 2px 4px 20px rgba(0, 0, 0, 0.1);
  position: relative;
  cursor: default;
  min-width: 26px;
  line-height: 22px;
  display: flex;
}
.highlight .delete {
  top: -15px;
  left: -13px;
  position: absolute;
  display: none;
}
.highlight:hover .delete {
  display: block;
}
.highlight__content {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  padding: 2px 2px 0px 6px;
}
.highlight.bottom .highlight__content:after {
  content: " ";
  padding-right: 3px;
}
.highlight__label {
  line-height: 14px;
  padding-top: 1px;
  align-items: center;
  justify-content: center;
  display: flex;
  padding: 0 8px;
  text-align: center;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  color: white;
}
.highlight__label::after {
  content: attr(data-label);
  display: block;
  font-size: 14px;
  -webkit-font-smoothing: subpixel-antialiased;
  letter-spacing: 0.1em;
}
</style>
