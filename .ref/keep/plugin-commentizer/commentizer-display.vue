<template>
  <div>
    <h2 v-text="setting('commentizer.displayText')" />
    <div v-if="!isEmpty(comments)">
      <div v-for="(comment, i) in comments" :key="`comment-${i}`">
        <span class="commentizer-content" v-text="comment.content" />
        &mdash;
        <a :href="`mailto:${comment.email}`" v-text="comment.name" />
      </div>
    </div>
    <div v-else class="commentizer">
      <div class="commentizer-content">No comments yet.</div>
    </div>
  </div>
</template>

<script>
import { isEmpty, setting, stored } from "@factor/tools"
import { requestPostSingle } from "@factor/post"
export default {
  props: {
    postId: { type: String, required: true }
  },
  data: () => {
    return {
      comments: []
    }
  },
  computed: {
    post() {
      return stored(this.postId) || {}
    }
    // TODO: Fix - Requires population to work!
    // comments() {
    //   return this.post.commentizerComments
    // }
  },
  async created() {
    // TODO: Fix - Manually populate comments
    this.comments = await Promise.all(
      this.post.commentizerComments.map(async (id) => {
        return await requestPostSingle({ postType: "commentizer", _id: id })
      })
    )
  },
  methods: { isEmpty, setting }
}
</script>
