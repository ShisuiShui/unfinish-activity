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
            <span class="ml-2">{{ formatDate(post.timestamp) }}</span>
          </v-card-title>
          <v-card-text>
            <v-list>
              <v-list-item>
                <v-list-item-content>{{ post.content }}</v-list-item-content>
              </v-list-item>
              <div class="d-flex justify-end mb-3">
                <v-icon @click="toggleLike(index)" :color="likedPosts[index] ? 'red' : 'red' ">mdi-heart</v-icon>
                <span class="ml-2">{{ likeCount[index] }}</span>
                <div class="">
                  <v-icon @click="toggleCommentForm(index)" :color="showCommentForm[index] ? 'blue' : 'grey' ">mdi-chat</v-icon>
                  <span class="ml-2">{{ comments[index].length }}</span>
                  <v-icon @click="deletePost(index)" color="orange">mdi-delete</v-icon>
                  <v-icon @click="editPost(index)" color="blue">mdi-pencil</v-icon>
                  <v-list-item-content>
                    <!-- Show textarea when editing post, otherwise show post content -->
                    <template v-if="editingPost.index === index">
                      <v-textarea v-model="editingPost.content" label="Edit post"></v-textarea>
                      <div class="d-flex justify-end mb-3">
                        <v-btn @click="updatePost(index)" color="primary">Update</v-btn>
                        <v-btn @click="cancelEditPost" color="grey">Cancel</v-btn>
                      </div>
                    </template>
                    <template v-else>
                      {{posts.content }}
                    </template>
                  </v-list-item-content>

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
                    <span class="ml-2">{{ formatDate(comment.timestamp) }}</span>
                  </div>
                  <v-list-item-content>
                    <!-- Show textarea when editing comment, otherwise show comment content -->
                    <template v-if="isEditingComment && editingComment.postIndex === index && editingComment.commentIndex === commentIndex">
                      <v-textarea v-model="editingComment.content" label="Edit comment"></v-textarea>
                    </template>
                    <template v-else>
                      {{ comment.content }}
                    </template>
                  </v-list-item-content>
                  <div class="d-flex justify-end mb-3">
                    <v-icon @click="togglecomment(index, commentIndex)" :color="commentLiked[index][commentIndex] ? 'red' : 'red'">mdi-heart</v-icon>
                    <span style="margin-left: 5px;">{{ commentLikeCount[index][commentIndex] || 0 }}</span>
                    <!-- Delete comments -->
                    <v-icon @click="deleteComment(index, commentIndex)" color="orange">mdi-delete</v-icon>
                    <v-icon @click="editComment(index, commentIndex, comment)" color="blue">mdi-pencil</v-icon>
                    <div class="d-flex justify-end mb-3">
                      <v-btn @click="updateComment" color="primary">Update</v-btn>
                    </div>
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
      commentLikeCount: [],
      editingPost: {
        index: -1,
        content: "",
      },
      editingComment: {
        postIndex: -1,
        commentIndex: -1,
        content: "",
      },
      isEditingComment: false,
    };
  },

  methods: {
    addPost() {
      if (this.postContent.trim() !== "") {
        const newPost = {
          content: this.postContent,
          timestamp: new Date(),
        };
        this.posts.unshift(newPost);
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
      // If not initialized, set it to false
      if (typeof this.commentLiked[postIndex] === 'undefined') {
        this.$set(this.commentLiked, postIndex, []);
      }

      const commentLikedArray = this.commentLiked[postIndex];

      // If not initialized, set it to false
      if (typeof commentLikedArray[commentIndex] === 'undefined') {
        this.$set(commentLikedArray, commentIndex, false);
      }

      // Toggle the like status (true or false)
      commentLikedArray[commentIndex] = !commentLikedArray[commentIndex];

      // Update the like count for the specific comment
      if (this.commentLikeCount[postIndex]) {
        const commentLikeCountArray = this.commentLikeCount[postIndex];

        // Update the like count for the specific comment, ensure it doesn't go below 0
        commentLikeCountArray[commentIndex] = Math.max(0, commentLikeCountArray[commentIndex] + (commentLikedArray[commentIndex] ? 1 : +1));
      }
    },

    toggleLike(index) {
      this.likedPosts[index] = !this.likedPosts[index];
      this.likeCount[index] += this.likedPosts[index] ? 1 : +1; // Update like count based on like status
    },
    toggleCommentForm(index) {
      this.showCommentForm[index] = !this.showCommentForm[index];
    },

    deleteComment(postIndex, commentIndex) {
      if (confirm("Are you sure you want to delete this post?"))
      // Remove the comment from the array
      this.comments[postIndex].splice(commentIndex, 1);
    },


    deletePost(index) {
      // Show a confirmation dialog before deleting the post
      if (confirm("Are you sure you want to delete this post?")) {
        // Remove the post and related data from the arrays
        this.posts.splice(index, 1);
        this.likedPosts.splice(index, 1);
        this.likeCount.splice(index, 1);
        this.showCommentForm.splice(index, 1);
        this.commentInput.splice(index, 1);
        this.comments.splice(index, 1);
        this.commentLiked.splice(index, 1);
        this.commentLikeCount.splice(index, 1);

        // You may also want to perform any additional cleanup or send a request to delete the post on the server
      }
    },
    editComment(postIndex, commentIndex, comment) {
      // Set the editingComment object to initialize the editing state
      this.editingComment = {
        postIndex,
        commentIndex,
        content: comment.content,
      };
      // Reset the like count for the specific comment
      if (this.commentLikeCount[postIndex]) {
        this.commentLikeCount[postIndex][commentIndex] = 0;
    }
      // Set isEditingComment to true
      this.isEditingComment = true;
    },

    editPost(index) {
      // Assuming you have some kind of editing mechanism, you can implement it here
      // For example, you can open a modal or update the post content in the UI for editing
      // You can use this.posts[index] to access the post data at the specified index
      // You can add an "Edit" button in your UI to trigger this method

      // Here's a simple example:
      const editedContent = prompt("Edit Your Post Here:", this.posts[index].content);
      if (editedContent !== null) {
        // Reset the liked status to false when editing the post
        this.likedPosts[index] = false;


        // Reset the like count to 0
        this.likeCount[index] = 0;

        this.posts[index].content = editedContent;
        // You can also perform additional logic such as sending an update request to the server
      }
    },

    updateComment() {
      // Assuming you have an "Update" button in your UI to trigger this method
      const { postIndex, commentIndex, content } = this.editingComment;
      // Reset the like count for the specific comment to 0
      if (this.commentLikeCount[postIndex]) {
        this.commentLikeCount[postIndex][commentIndex] = 0;
      }
      // Update the comment content
      this.comments[postIndex][commentIndex].content = content;

      // Reset the editing state
      this.cancelEditComment();
    },
    cancelEditComment() {
      // Reset the editing state
      this.editingComment = {
        postIndex: -1,
        commentIndex: -1,
        content: "",
      };
      // Set isEditingComment to false
      this.isEditingComment = false;
    },
    updatePost(index) {
      // Assuming you have an "Update" button in your UI to trigger this method
      this.posts[index].content = this.editingPost.content;

      // Reset the editing state
      this.cancelEditPost();
    },

    cancelEditPost() {
      // Reset the editing state for the main post
      this.editingPost = {
        index: -1,
        content: "",
      };
    },


    addComment(index) {
      const commentText = this.commentInput[index];
      if (commentText.trim() !== "") {
        const newComment = {
          content: commentText,
          timestamp: new Date(),
        };
        this.comments[index].unshift(newComment);

        // Ensure that commentLiked and commentLikeCount arrays are initialized
        if (!Array.isArray(this.commentLiked[index])) {
          this.$set(this.commentLiked, index, []);
        }
        if (!Array.isArray(this.commentLikeCount[index])) {
          this.$set(this.commentLikeCount, index, []);
        }

        this.commentLiked[index].unshift([]);
        this.commentLikeCount[index].unshift(0); // Initialize the like count to 0
        this.commentInput[index] = "";
      }
    },
    formatDate(timestamp) {
      const options = {
        year: "numeric",
        month: "numeric",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
        hour12: true,
      };

      return new Date(timestamp).toLocaleString("en-US", options);
    },
  },
});
</script>

<style scoped>
/* Add any additional styling if needed */
</style>
