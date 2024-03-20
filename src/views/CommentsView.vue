<template>
    <div class="container mt-5">
      <div class="mb-3">
        <label for="URLVideoId" class="form-label">YouTube URL Video</label>
        <input type="text" class="form-control" id="URLVideoId" v-model="URLVideoId">
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
  import { apiBaseUrl } from '../config'
  
  export default {
    setup() {
      const URLVideoId = ref('');
      const comments = ref([]);
  
      const fetchComments = async () => {
        try {
          const token = Cookies.get('userToken');
          console.log(URLVideoId.value)
          const videoId = extractYouTubeVideoID(URLVideoId.value)
          const response = await axios.get(`${apiBaseUrl}/comments/${videoId}`, {
            headers: { Authorization: `Bearer ${token}` },
          });
          comments.value = response.data;
        } catch (error) {
          console.error(error);
          alert('Failed to fetch comments!');
        }
      };

      const extractYouTubeVideoID = function(url) {
        try {
            console.log(url);
            const urlObj = new URL(url);
            if (urlObj.hostname === 'www.youtube.com' || urlObj.hostname === 'youtube.com') {
            return urlObj.searchParams.get('v');
            }
            else if (urlObj.hostname === 'youtu.be') {
                return urlObj.pathname.substring(1);
            }
            else {
                return null;
            }
        } catch (error) {
            console.error('Invalid URL:', error);
            alert('Invalid URL!');
            return null;
        }
      };
  
      return { URLVideoId: URLVideoId, comments, fetchComments };
    }
  };
  </script>
  