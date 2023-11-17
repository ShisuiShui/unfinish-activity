<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="mb-4 display-1">Post</h1>

        <!-- Post content textarea -->
        <v-textarea v-model="postContent" label="What's on your mind?" />

        <!-- Button to submit the post -->
        <div class="d-flex justify-end mb-3">
          <v-btn @click="addPost" color="primary">Post</v-btn>
        </div>
        <!--display post-->
        <v-card v-if="posts.length > 0" v-for="(post, index) in posts" :key="index" class="mb-5">
          <v-card-title class = "d-flex">
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
              </div>
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

    };

  },
  methods: {
    addPost() {
      if (this.postContent.trim() !== "") {
        this.posts.unshift(this.postContent);
        this.likedPosts.unshift(false);
        this.likeCount.unshift(0);
        this.postContent = "";
      }
    },
    toggleLike(index) {
      this.likedPosts[index] = !this.likedPosts[index];
      this.likeCount[index] += this.likedPosts[index] ? 1 : +1;
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
