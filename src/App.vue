<script setup>
import {ref, computed, onMounted} from 'vue';


import PaginatePost from './components/PaginatePost.vue';
import BlogPost from './components/BlogPost.vue';
import LoadingSpinner from './components/LoadingSpinner.vue';
const posts = ref([])
const postPorPagina = 10
const inicio = ref(0)
const fin = ref(postPorPagina)
const loading = ref(true);

const favorito = ref('')

const cambiarFavorito = (post) => {
  favorito.value = post
}

const next = () => {
  inicio.value = inicio.value + postPorPagina;
  fin.value = fin.value + postPorPagina;
}
const prev = () => {
  inicio.value = inicio.value - postPorPagina;
  fin.value = fin.value - postPorPagina;
}

onMounted(async() => {
  loading.value = true;
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts')
    posts.value = await res.json();
  } catch (error) {
      console.log(error);
  } finally {
    setTimeout(() => {
      loading.value = false;
    }, 1000);
  }
})

// fetch('https://jsonplaceholder.typicode.com/posts')
//   .then(res => res.json())
//   .then((data) => {
//     posts.value = data;
//   })
//   .catch((e) => console.log(e))
//   .finally(() => {
//     setTimeout(() => {
//       loading.value = false;
//     }, 1000);
//   })

  const maxLength = computed(()=> posts.value.length)
</script>

<template>
    <LoadingSpinner v-if="loading" />
  <div class="container" v-else>
    <h1>APP</h1>
    <h2>Mis post favoritos:{{favorito}} </h2>

    <PaginatePost 
    @next="next()" 
    @prev="prev()" 
    :inicio="inicio" 
    :fin="fin" 
    class="mb-2"
    :maxLength="maxLength"
    />

    <BlogPost 
    v-for="post in posts.slice(inicio,fin)"
    :key="post.id"
    :title="post.title" 
    :id="post.id" 
    :body="post.body"
    @cambiarFavoritoNombre="cambiarFavorito"
    class="mb-2"
    />

  </div>
</template>

<style></style>