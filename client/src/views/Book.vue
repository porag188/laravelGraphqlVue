<template>
  <div class="Book">
    <ApolloQuery :query="require('@/graphql/queries/Book.gql')" :variables="{id:$route.params.id}">
      <template slot-scope="{ result: { data, loading },isLoading}">
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <div>
            <img :src="`http://127.0.0.1:8000/img/${data.book.image}`" alt="Cover Image" />
          </div>
          <div>{{data.book.title}}</div>
          <div>{{data.book.author}}</div>
          <router-link :to="`/books/${data.book.id}/edit`">Edit</router-link>||
          <a href="#" class="link-margin" @click.prevent="deleteBook">Delete</a>
        </div>
      </template>
    </ApolloQuery>
  </div>
</template>
<script>
import deleteBook from "@/graphql/mutation/DeleteBook.gql";
export default {
  methods: {
    deleteBook() {
      this.$apollo
        .mutate({
          mutation: deleteBook,
          variables: {
            id: this.$route.params.id
          }
        })
        .then(data => {
          console.log(data);
          this.$router.push("/");
        });
    }
  }
};
</script>