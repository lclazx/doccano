<template>
  <spo-entity-item-box
    v-if="isReady"
    :labels="labels"
    :relationLabels="items"
    :text="currentDoc.text"
    :entities="labeledWords"
    :delete-annotation="removeEntity"
    :update-entity="updateEntity"
    :add-entity="addEntity"
  />
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
          id: 2,
          prefix_key: null,
          suffix_key: 'b',
          text: '谓语',
          text_color: '#ffffff'
        }
      ],
      labeledWords: []
    }
  },
  computed: {
    ...mapState('labels', ['items', 'loading']),
    ...mapGetters('documents', ['currentDoc']),
    isReady() {
      return !!this.currentDoc && !this.loading
    }
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
        projectId: this.$route.params.id
      }
      this.labeledWords.push(payload)
      // this.addAnnotation(payload)
    }
  }
}
</script>

<style></style>
