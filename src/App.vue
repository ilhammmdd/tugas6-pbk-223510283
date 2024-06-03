<template>
  <div class="container">
    <h1>TUGAS 6 - ILHAM</h1>
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Title" class="input-field" />
      <textarea v-model="form.content" placeholder="Content" class="textarea-field"></textarea>
      <button type="submit" class="btn-save">Save</button>  
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <div class="btn-container">
          <button @click="edit(article)" class="btn-edit">Edit</button>
          <button @click="deleteArticle(article.id)" class="btn-delete">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="btn-load">Load</button>
  </div>
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
    const url = form.id
      ? `http://localhost:3000/articles/${form.id}`
      : 'http://localhost:3000/articles';
    const method = form.id ? 'put' : 'post';

    if (!form.id) {
      const highestId = articles.value.reduce((maxId, article) => {
        return article.id > maxId ? article.id : maxId;
      }, 0);
      form.id = highestId !== 0 ? highestId  + 1 : 1; 
    }
    const response = await axios[method](url, form);

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

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
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

    return { form, articles, save, edit, deleteArticle, load };
  }
}
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #F5F7F8;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.form {
  margin-bottom: 20px;
}

.input-field, .textarea-field {
  width: calc(100% - 20px);
  padding: 12px;
  margin-bottom: 10px;
  border: none;
  background-color: #f9f9f9;
  border-radius: 6px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: background-color 0.3s, box-shadow 0.3s;
  color: black;
}

.input-field:focus, .textarea-field:focus {
  background-color: #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.input-field::placeholder, .textarea-field::placeholder {
  color: black;
}

.btn-save, .btn-edit, .btn-delete, .btn-load {
  background-color: #F4CE14;
  color: white;
  border: none;
  padding: 12px 20px;
  cursor: pointer;
  border-radius: 6px;
  transition: background-color 0.3s;
}

.btn-save:hover, .btn-edit:hover, .btn-delete:hover, .btn-load:hover {
  background-color: #4d4209;
}

.article {
  background-color: #fff;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.article h3 {
  font-size: 24px;
  margin-bottom: 10px;
  color: #333;
}

.article p {
  font-size: 18px;
  color: #666;
  line-height: 1.6;
}

.btn-container {
  margin-top: 10px;
}

.btn-edit, .btn-delete {
  margin-right: 10px;
}

.btn-edit {
  background-color: #495E57;
}

.btn-delete {
  background-color: #45474B;
}

.btn-edit:hover {
  background-color: #272727;
}

.btn-delete:hover {
  background-color: #600f16;
}

.article h3 {
  font-size: 24px;
  margin-bottom: 10px;
  color: #333;
  display: inline; 
}

.article p {
  font-size: 18px;
  color: #666;
  line-height: 1.6;
  display: inline; 
}

.article h3::after,
.article p::after {
  content: ''; 
}

h1{
  color: black;
}
</style>