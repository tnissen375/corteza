<template>
  <div>
    <div
      v-for="(feed, i) in options.feeds"
      :key="i"
    >
      <div
        v-if="feed.resource"
        class="d-flex justify-content-end mb-3"
      >
        <c-input-confirm
          show-icon
          size="md"
          @confirmed="onRemoveFeed(i)"
        />
      </div>

      <b-form-group horizontal>
        <component
          :is="configurator(feed)"
          v-if="feed.resource && configurator(feed)"
          :feed="feed"
          :modules="modules"
        />
      </b-form-group>

      <hr>
    </div>

    <b-button
      variant="primary"
      class="test-feed-add"
      @click.prevent="handleAddButton"
    >
      {{ $t('geometry.addSource') }}
    </b-button>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import base from '../../base'
import * as configs from './configs'
import { compose } from '@cortezaproject/corteza-js'

export default {
  i18nOptions: {
    namespaces: 'block',
  },

  components: {
  },

  extends: base,

  computed: {
    ...mapGetters({
      modules: 'module/set',
    }),

    /**
     * Provides a set of available feed sources.
     * @returns {Array}
     */
    feedSources () {
      return Object.entries(compose.PageBlockGeometry.feedResources).map(([key, value]) => ({
        value,
        text: this.$t(`geometry.${key}Feed.optionLabel`),
      }))
    },
  },

  created () {
    if (this.options.feeds.length === 0) {
      this.block.options.feeds = []
    }
  },

  methods: {
    /**
     * Handles feed removal
     * @param {Number} i Feed's index
     */
    onRemoveFeed (i) {
      this.block.options.feeds.splice(i, 1)
    },

    /**
     * Handles feed's addition
     */
    handleAddButton () {
      this.block.options.feeds.push(compose.PageBlockGeometry.makeFeed())
    },

    /**
     * configurator uses feed's resource to determine what configurator to use.
     * If it can't find an apropriate component, undefined is returned
     * @param {Feed} feed Feed in qestion
     * @returns {Component|undefined}
     */
    configurator (feed) {
      if (!feed.resource) {
        return
      }
      const r = feed.resource.split(':').pop()
      return configs[r]
    },
  },
}
</script>
