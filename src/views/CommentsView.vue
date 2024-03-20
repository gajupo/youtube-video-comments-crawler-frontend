<template>
    <div class="container mt-5">
      <div class="mb-3">
        <label for="videoId" class="form-label">YouTube Video ID</label>
        <input type="text" class="form-control" id="videoId" v-model="videoId">
      </div>
      <button @click="fetchComments" class="btn btn-primary">Get Comments</button>
  
      <table class="table table-hover table-striped">
        <thead>
            <tr>
            <th scope="col">Author</th>
            <th scope="col">Comment</th>
            <th scope="col">Published At</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="comment in comments" :key="comment.id">
            <td class="text-wrap" style="max-width: 150px;">{{ comment.author }}</td>
            <td class="text-wrap" style="max-width: 300px;">{{ comment.comment }}</td>
            <td>{{ new Date(comment.publishedAt).toLocaleDateString() }}</td>
            </tr>
        </tbody>
        </table>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  import axios from 'axios';
  import Cookies from 'js-cookie';
  
  export default {
    setup() {
      const videoId = ref('');
      const comments = ref([]);
  
      const fetchComments = async () => {
        try {
          const token = Cookies.get('userToken');
          const response = await axios.get(`http://localhost:8081/api/comments/${videoId.value}`, {
            headers: { Authorization: `Bearer ${token}` },
          });
          comments.value = response.data;
        } catch (error) {
          console.error(error);
          alert('Failed to fetch comments!');
        }
      };
  
      return { videoId, comments, fetchComments };
    }
  };
  </script>
  