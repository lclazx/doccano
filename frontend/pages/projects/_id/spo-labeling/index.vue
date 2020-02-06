<template>
  <v-card>
    <v-card-text class="title">
      <spo-container />
    </v-card-text>
  </v-card>
</template>

<script>
import SpoContainer from '~/components/containers/annotation/SpoContainer'

export default {
  layout: 'annotation',

  components: {
    SpoContainer
  }
}
</script>
