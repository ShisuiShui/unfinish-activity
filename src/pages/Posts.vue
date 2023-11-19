<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="mb-4 display-1">Post</h1>
        <!-- Post content textarea -->
        <v-textarea auto-grow v-model="postContent" label="What's on your mind?" />
        <!-- Button to submit the post -->
        <div class="d-flex justify-end mb-3">
          <v-btn @click="addPost" color="primary">Post</v-btn>
        </div>
        <!-- Display posts -->
        <v-card v-for="(post, index) in posts" :key="index" class="mb-5">
          <v-card-title class="d-flex">
            <img src="dist/assets/images.jfif" alt="" class="image ml-2" />
            <span class="ml-2">Henry</span>
            <v-spacer></v-spacer>
            <span class="ml-2">{{ formatDate() }}</span>
          </v-card-title>
          <v-card-text>
            <v-list>
              <v-list-item>
                <v-list-item-content>{{ post }}</v-list-item-content>
              </v-list-item>
              <div class="d-flex justify-end mb-3">
                <v-icon @click="toggleLike(index)" :color="likedPosts[index] ? 'red' : 'red' ">mdi-heart</v-icon>
                <span class="ml-2">{{ likeCount[index] }}</span>
                <div class="">
                  <v-icon @click="toggleCommentForm(index)" :color="showCommentForm[index] ? 'blue' : 'grey' ">mdi-chat</v-icon>
                  <span class="ml-2">{{ comments[index].length }}</span>
                </div>
              </div>
              <!-- Comment input and submit button -->
              <v-row v-if="showCommentForm[index]">
                <v-col>
                  <v-textarea v-model="commentInput[index]" label="Add a comment" />
                  <div class="d-flex justify-end mb-3">
                    <v-btn @click="addComment(index)" color="primary">Comment</v-btn>
                  </div>
                </v-col>
              </v-row>
              <!-- Display comments -->
              <v-list v-if="comments[index].length > 0" style="margin-top: 5px;">
                <v-list-item v-for="(comment, commentIndex) in comments[index]" :key="commentIndex" style="border: 5px solid #ccc; padding: 5px; margin-bottom: 5px;">
                  <v-icon>mdi-account</v-icon>
                  <span class="ml-2">Henry</span>
                  <div class="d-flex justify-end mb-3">
                    <span class="ml-2">{{ formatDate() }}</span>
                  </div>
                  <!-- react for comments/showing comments -->
                  <v-list-item-content>{{ comment }}</v-list-item-content>
                  <div class="d-flex justify-end mb-3">
                    <v-icon @click="togglecomment(index, commentIndex)" :color="commentLiked[index][commentIndex] ? 'red' : 'red' ">mdi-heart</v-icon>
                    <span style="margin-left: 5px;">{{ commentLikeCount[index][commentIndex] }}</span>
                  </div>
                </v-list-item>
              </v-list>
            </v-list>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: "Post",
  data() {
    return {
      postContent: "",
      posts: [],
      likedPosts: [],
      likeCount: [],
      showCommentForm: [],
      commentInput: [],
      comments: [],
      commentLiked: [],
      commentLikeCount: [], // Use an array of arrays for comment like counts
    };
  },
  methods: {
    addPost() {
      if (this.postContent.trim() !== "") {
        this.posts.unshift(this.postContent);
        this.likedPosts.unshift(false);
        this.likeCount.unshift(0);
        this.showCommentForm.unshift(false);
        this.commentInput.unshift("");
        this.comments.unshift([]);
        this.commentLiked.unshift([]);
        this.commentLikeCount.unshift([]);
        this.postContent = "";
      }
    },
    togglecomment(postIndex, commentIndex) {
      this.$set(this.commentLiked[postIndex], commentIndex, !this.commentLiked[postIndex][commentIndex]);

      // Update the like count for the specific comment
      this.$set(
          this.commentLikeCount[postIndex],
          commentIndex,
          this.commentLiked[postIndex][commentIndex]
              ? (this.commentLikeCount[postIndex][commentIndex] || 0) + 1
              : (this.commentLikeCount[postIndex][commentIndex] || 0)
      );
    },
    toggleLike(index) {
      this.likedPosts[index] = !this.likedPosts[index];
      this.likeCount[index] += this.likedPosts[index] ? 1 : +1; // Update like count based on like status
    },
    toggleCommentForm(index) {
      this.showCommentForm[index] = !this.showCommentForm[index];
    },
    addComment(index) {
      const comment = this.commentInput[index];
      if (comment.trim() !== "") {
        this.comments[index].unshift(comment);
        this.commentLiked[index].unshift([]);
        this.commentLikeCount[index].unshift([]);
        this.commentInput[index] = "";
      }
    },
    formatDate() {
      const now = new Date();
      const options = {
        year: "numeric",
        month: "numeric",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
        hour12: true,
      };
      return now.toLocaleString("en-US", options);
    },
  },
});
</script>

<style scoped>
/* Add any additional styling if needed */
</style>
