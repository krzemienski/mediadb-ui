<template>
  <div class="container fluid">
    <q-btn-group
      class="q-pb-md"
      unelevated
    >
      <filters
        :namespace="namespace"
        field="sort"
        :options="sorters"
      />
    </q-btn-group>

    <infinite
      :namespace="namespace"
      item-component="Video"
    />
  </div>
</template>

<script>
import paginateModule from 'src/store/paginate'

export default {
  components: {
    Infinite: () => import('components/paginate/Infinite'),
    Filters: () => import('components/paginate/Filters')
  },

  props: {
    data: {
      type: Object,
      required: true
    },

    meta: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      namespace: `channel_${this.data.id}`,
      apiRoute: {
        path: 'media',
        params: {
          include: 'model,tags',
          'filter[channel]': this.data.id,
          'page[size]': 12,
          sort: 'recent'
        }
      },
      sorters: [
        { label: 'Most recent', value: 'recent' },
        { label: 'Most viewed', value: 'views' },
        { label: 'At Random', value: 'recommended' },
        { label: 'Alphabetical', value: 'name' },
        { label: 'Shortest to Longest', value: 'shortest' },
        { label: 'Longest to Shortest', value: 'longest' }
      ]
    }
  },

  async created () {
    if (!this.$store.hasModule(this.namespace)) {
      this.$store.registerModule(this.namespace, paginateModule)

      // Fetch first page
      await this.$store.dispatch(this.namespace + '/create', this.apiRoute)
    }
  }
}
</script>
