<template>
  <div>
    <QInput
      :value="terms"
      type="search"
      :placeholder="$q.lang.label.search"
      dense
      autofocus
      :debounce="300"
      standout
      dark
      @input="setTerms"
      @click="showResults"
      @focus="showResults"
      @blur="hide"
      @keyup.esc="hide"
    >
      <template #prepend>
        <QIcon name="search" />
      </template>
      <template #append>
        <QIcon
          name="cancel"
          class="cursor-pointer"
          @click="clear"
        />
      </template>
      <QMenu
        ref="menu"
        no-parent-event
        no-focus
        fit
      >
        <QList>
          <QInfiniteScroll @load="onLoad">
            <QItem
              v-for="(result, idx) in results.slice(0, limit)"
              :key="idx"
              v-close-popup
              :to="result.value"
              clickable
              @click="hide"
            >
              <QItemSection
                v-if="result.icon"
                side
              >
                <QIcon :name="result.icon" />
              </QItemSection>
              <QItemSection
                v-if="result.user"
                side
              >
                <ProfilePicture
                  :user="result.user"
                  :is-link="false"
                  :size="25"
                />
              </QItemSection>
              <QItemSection>
                <QItemLabel>
                  {{ result.label }}
                </QItemLabel>
                <QItemLabel caption>
                  {{ result.sublabel }}
                </QItemLabel>
              </QItemSection>
            </QItem>
          </QInfiniteScroll>
          <QItem
            v-if="results.length < 1"
          >
            <QItemSection>
              <QItemLabel>
                {{ $t('GLOBAL.SEARCH_NOT_FOUND') }}
              </QItemLabel>
            </QItemSection>
          </QItem>
        </QList>
      </QMenu>
    </QInput>
  </div>
</template>

<script>
import ProfilePicture from '@/users/components/ProfilePicture'

import {
  QInput,
  QIcon,
  QMenu,
  QList,
  QItem,
  QItemSection,
  QItemLabel,
  QInfiniteScroll,
} from 'quasar'
import { mapMutations, mapGetters } from 'vuex'

export default {
  components: {
    ProfilePicture,
    QInput,
    QIcon,
    QMenu,
    QList,
    QItem,
    QItemSection,
    QItemLabel,
    QInfiniteScroll,
  },
  data () {
    return {
      limit: 10,
    }
  },
  computed: {
    ...mapGetters({
      results: 'search/results',
      terms: 'search/terms',
    }),
  },
  watch: {
    results () {
      // reset limit if results change
      this.limit = 10
    },
  },
  methods: {
    ...mapMutations({
      setTerms: 'search/setTerms',
      hide: 'search/hide',
    }),
    clear () {
      this.hide()
      this.hideResults()
      this.setTerms(null)
      this.$emit('clear')
    },
    showResults () {
      this.$refs.menu.show()
    },
    hideResults () {
      this.$refs.menu.hide()
    },
    onLoad (index, done) {
      this.limit += 10
      done()
    },
  },
}
</script>
