<template>
  <v-container fluid>
    <template v-if="currentPost">
      <v-breadcrumbs :items="breadcrumbs" :add-items="addBreads">
        <template #divider>
          <v-icon>mdi-chevron-right</v-icon>
        </template>
      </v-breadcrumbs>
      
      {{ currentPost.fields.title }}
      <v-img
        :src="setEyeCatch(currentPost).url"
        :alt="setEyeCatch(currentPost).title"
        :aspect-ratio="16/9"
        width="700"
        height="400"
        class="mx-auto"
      />
      {{ currentPost.fields.publishDate }}<span :is="draftChip(currentPost)" /><br>
      {{ currentPost.fields.body }}
    </template>

    <template v-else>
      お探しの記事は見つかりませんでした。
    </template>

    <div>
      <v-btn
        outlined
        color="primary"
        to="/"
      >
        <v-icon size="16">
          fas fa-angle-double-left
        </v-icon>
        <span class="ml-1">ホームへ戻る</span>
      </v-btn>
    </div>
  </v-container>
</template>

<script>
import draftChip from '~/components/posts/draftChip'
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters(['setEyeCatch','draftChip','linkTo']),
    breadcrumbs() {
      const category = this.currentPost.fields.category
      return [
        { text: 'ホーム', to: '/' },
        { text: category.fields.name, to: '#' }
      ]
    },
    addBreads() {
      return [
        {
          icon: 'mdi-folder-outline',
          text: this.category.fields.name,
          to: this.linkTo('categories', this.category)
        }
      ]
    }
  },
  components: {
    draftChip
  },
  async asyncData({ payload, store, params, error }) {
    const currentPost = payload || await store.state.posts.find(post => post.fields.slug === params.slug)

    if (currentPost) {
      return { 
        currentPost,
        category: currentPost.fields.category
      }
    } else {
      return error({ statusCode: 400 })
    }
  }
}
</script>