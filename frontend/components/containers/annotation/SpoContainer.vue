<template>
  <div>
    <v-card>
      <spo-entity-item-box
        v-if="isReady"
        :labels="labels"
        :relationLabels="items"
        :text="currentDoc.text"
        :entities="computedLabeledWords"
        :delete-annotation="removeEntity"
        :update-entity="updateEntity"
        :add-entity="addEntity"
        :make-relation="makeRelation"
      />
    </v-card>
    <v-divider />
    <v-card>
      <v-data-table
        v-model="selected"
        :headers="headers"
        :items="relations"
        show-select
      >
        <!-- <template v-slot:top>
          <v-btn type="primary">
            删除
          </v-btn>
        </template> -->
      </v-data-table>
    </v-card>
  </div>
</template>

<script>
import { mapActions, mapGetters, mapState } from 'vuex'
import SpoEntityItemBox from '~/components/organisms/annotation/SpoEntityItemBox'
export default {
  components: { SpoEntityItemBox },
  data() {
    return {
      labels: [
        {
          background_color: '#00ff00',
          id: 1,
          prefix_key: null,
          suffix_key: 'a',
          text: '主语',
          text_color: '#ffffff'
        },
        {
          background_color: '#ff0000',
          id: 0,
          prefix_key: null,
          suffix_key: 'b',
          text: '宾语',
          text_color: '#ffffff'
        }
      ],
      labeledWords: [],
      selected: [],
      headers: [
        { text: '主语', align: 'left', sortable: false, value: 'subject_text' },
        { text: '宾语', align: 'center', sortable: false, value: 'object_text' },
        { text: '关系', align: 'right', sortable: false, value: 'relation_text' }
      ]

    }
  },
  computed: {
    ...mapState('labels', ['items', 'loading']),
    ...mapGetters('documents', ['currentDoc']),
    isReady() {
      return !!this.currentDoc && !this.loading
    },
    computedLabeledWords() {
      const words = []
      for (const annotation of this.currentDoc.annotations) {
        if (!(words.find(x => x.start_offset === annotation.subject_start_offset && x.end_offset === annotation.subject_end_offset))) {
          const subject = {
            start_offset: annotation.subject_start_offset,
            end_offset: annotation.subject_end_offset,
            label: 1,
            projectId: this.$route.params.id,
            text: this.currentDoc.text.slice(annotation.subject_start_offset, annotation.subject_end_offset)
          }
          words.push(subject)
        }
        if (!(words.find(x => x.start_offset === annotation.object_start_offset && x.end_offset === annotation.object_end_offset))) {
          const obj = {
            start_offset: annotation.object_start_offset,
            end_offset: annotation.object_end_offset,
            label: 0,
            projectId: this.$route.params.id,
            text: this.currentDoc.text.slice(annotation.object_start_offset, annotation.object_end_offset)
          }
          words.push(obj)
        }
      }
      for (const word of this.labeledWords) { words.push(word) }

      return words
    },
    relations() {
      const result = []
      if (this.currentDoc) {
        for (const annotation of this.currentDoc.annotations) {
          result.push({
            annotation: annotation,
            subject_text: this.currentDoc.text.slice(annotation.subject_start_offset, annotation.subject_end_offset),
            object_text: this.currentDoc.text.slice(annotation.object_start_offset, annotation.object_end_offset),
            relation_text: this.items.find(x => x.id === annotation.label).text
          }
          )
        }
      }
      // return [{
      //   subject_text: '阿兰斯基',
      //   object_text: '古巴',
      //   relation_text: '国籍'
      // }]
      return result
    }
  },
  mounted() {

  },
  created() {
    this.getLabelList({ projectId: this.$route.params.id })
  },
  methods: {
    ...mapActions('labels', ['getLabelList']),
    ...mapActions('documents', [
      'getDocumentList',
      'deleteAnnotation',
      'updateAnnotation',
      'addAnnotation'
    ]),
    removeEntity(annotationId) {
      const payload = {
        annotationId,
        projectId: this.$route.params.id
      }
      this.deleteAnnotation(payload)
    },
    updateEntity(labelId, annotationId) {
      const payload = {
        annotationId,
        label: labelId,
        projectId: this.$route.params.id
      }
      this.updateAnnotation(payload)
    },
    addEntity(startOffset, endOffset, labelId) {
      const payload = {
        start_offset: startOffset,
        end_offset: endOffset,
        label: labelId,
        projectId: this.$route.params.id,
        text: this.currentDoc.text.slice(startOffset, endOffset)
      }
      this.labeledWords.push(payload)
      // this.addAnnotation(payload)
    },
    makeRelation(subject, obj, relation) {
      const payload = {
        projectId: this.$route.params.id,
        subject_start_offset: subject.start_offset,
        subject_end_offset: subject.end_offset,
        object_start_offset: obj.start_offset,
        object_end_offset: obj.end_offset,
        label: relation.id
      }
      for (const item of this.currentDoc.annotations) {
        if (
          payload.subject_start_offset === item.subject_start_offset &&
payload.subject_end_offset === item.subject_end_offset &&
payload.object_start_offset === item.object_start_offset &&
payload.object_end_offset === item.object_end_offset) {
          this.updateEntity(payload, item.id)
          return
        }
      }
      this.addAnnotation(payload)
    }
  }
}
</script>

<style></style>
