<template>
  <div>
    <h1>Posts</h1>

    <input type="text" placeholder="title" v-model="title" class="title-input" />
    <input type="text" placeholder="body" v-model="body" class="body-input" />

    <button v-if="isEditing" @click="updatePost">Update</button>
    <button v-if="isCancel" @click="CancelEdit">Cancel</button>

    <button v-else @click="createPost">Create Post</button>

    <div class ="card-post" v-for="post in posts" :key="post.id">
      <h3>{{ post.title }}</h3>
      <p>{{ post.body }}</p>
      <button @click="editPost(post.id)">Edit</button>
      <button @click="deletePost(post.id)">Delete</button>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';


const posts = ref([]);
const post_id = ref(0);

const title = ref('');
const body = ref('');
const isEditing = ref(false);
const isCancel = ref(false);

const API_URL = 'http://localhost:3000/posts';

onMounted(async () => {
  const res = await fetch(API_URL);
  posts.value = await res.json();
});


const createPost = async () => {
  const res = await fetch(API_URL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ title: title.value, body: body.value }),
  });
  const newPost = await res.json();
  posts.value.push(newPost);
  title.value = '';
  body.value = '';
  post_id.value = 0;
}

const deletePost = async (id) => {
  const res = await fetch(`${API_URL}/${id}`, {
    method: 'DELETE',
  });
  posts.value = posts.value.filter((post) => post.id !== id);

}

const editPost = async (id) => {
  
  const res = await fetch(`${API_URL}/${id}`);
  const post = await res.json();
  title.value = post.title;
  body.value = post.body;
  post_id.value = post.id;
  isEditing.value = true;
  isCancel.value = true;
}


const updatePost = async () => {
    const res = await fetch(`${API_URL}/${post_id.value}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ title: title.value, body: body.value }),
    });
    const newPost = await res.json();
    posts.value = posts.value.map((post) => (post.id === id ? newPost : post));
    title.value = '';
    body.value = '';
    post_id.value = 0;
    isEditing.value = false;
    isCancel.value = false;

}
const CancelEdit = async () => {
  return true;
}
</script>

<style scoped>
.title-input {
  width: 100%;
  padding: 0.5em;
  font-size: 1.5em;
  border: 1px solid #ccc;
  border-radius: 0.5em;
}

.body-input {
  width: 100%;
  padding: 0.5em;
  font-size: 1.5em;
  border: 1px solid #ccc;
  border-radius: 0.5em;
}

.card-post {
  margin: 1em 0;
  padding: 1em;
  border: 1px solid #ccc;
  border-radius: 0.5em;
}
</style>
