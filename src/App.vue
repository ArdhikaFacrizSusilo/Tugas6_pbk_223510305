<template>
  <div class="container">
    <h1>Manajer Artikel</h1>
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Enter Title" class="input"/><br />
      <textarea v-model="form.content" placeholder="Enter Content" class="textarea"></textarea><br />
      <button type="submit" class="button">Save</button>  
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <div class="article-content">
          <h2>{{ article.title }}</h2>
          <p>{{ article.content }}</p>
        </div>
        <div class="article-actions">
          <button @click="edit(article)" class="button edit-button">Edit</button>
          <button @click="remove(article.id)" class="button delete-button">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="button load-button">Load</button>
  </div>
  <footer class="footer bg-pink-9 text-white">
    <p>Ardhika Facriz Susilo | 223510305</p>
  </footer>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: ''
    });
    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        let url = 'http://localhost:3000/articles';
        let method = 'post';
        const data = {
          title: form.title,
          content: form.content
        };

        if (form.id) {
          url = `http://localhost:3000/articles/${form.id}`;
          method = 'put';
          data.id = form.id;
        }

        const response = await axios[method](url, data);

        if (form.id) {
          articles.value = articles.value.map((article) =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  }
}
</script>

<style>
body {
  font-family: 'Arial', sans-serif;
  background-color: #254336;
  color: #333;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 800px;
  margin: 40px auto;
  padding: 20px;
  background-color: #6B8A7A;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}

h1 {
  text-align: center;
  color: #333;
}

.form {
  margin-bottom: 20px;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #DAD3BE;
}

.input, .textarea {
  width: calc(100% - 16px);
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  background-color: #007BFF;
  color: white;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #DAD3BE;
  margin-bottom: 10px;
}

.article-content h2 {
  margin: 0 0 10px 0;
  font-size: 1.5em;
  color: #007BFF;
}

.article-content p {
  margin: 0;
}

.article-actions {
  display: flex;
  align-items: center;
}

.edit-button, .delete-button {
  padding: 8px 12px;
  margin-left: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.edit-button {
  background-color: #28a745;
}

.edit-button:hover {
  background-color: #218838;
}

.delete-button {
  background-color: #dc3545;
}

.delete-button:hover {
  background-color: #c82333;
}

.load-button {
  margin-top: 20px;
}

.footer {
  background-color: #6B8A7A;
  color: #000000;
  padding: 1px 0;
  text-align: center;
}
</style>
