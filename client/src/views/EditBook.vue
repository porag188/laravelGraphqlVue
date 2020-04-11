<template>
  <div class="create">
    <h1>Edit Book</h1>
    <form action="#" method="POST" @submit.prevent="editBook">
      <div class="form-group">
        <label for="title">Title</label>
        <input type="text" name="title" id="title" v-model="title" />
      </div>

      <div class="form-group">
        <label for="author">Author</label>
        <input type="text" name="author" id="author" v-model="author" />
      </div>

      <div class="form-group">
        <label for="image">Image</label>
        <input type="text" name="image" id="image" v-model="image" />
      </div>

      <div class="form-group">
        <label for="description">Description</label>
        <textarea
          type
          name="description"
          id="description"
          cols="30"
          rows="10"
          v-model="description"
        ></textarea>
      </div>
      <div class="form-group">
        <label for="link">Link</label>
        <input type="text" name="link" id="link" v-model="link" />
      </div>
      <div class="form-group">
        <label>
          <input type="checkbox" name="featured" v-model="featured" />Featured
        </label>
      </div>
      <div class="form-group">
        <ApolloQuery :query="require('@/graphql/queries/Categories.gql')">
          <template slot-scope="{ result: { data, loading },isLoading}">
            <div v-if="isLoading">Loading...</div>
            <select v-else v-model="category_id">
              <option
                v-for="category of data.categories"
                :key="category.id"
                :value="category.id"
              >{{category.name}}</option>
            </select>
          </template>
        </ApolloQuery>
      </div>

      <div class="form-group">
        <button type="submit">Update Book</button>
      </div>
    </form>
  </div>
</template>

<script>
import updateBook from "@/graphql/mutation/EditBook.gql";
import book from "@/graphql/queries/Book.gql";
export default {
  data() {
    return {
      title: "",
      author: "",
      image: "",
      description: "",
      link: "",
      featured: false,
      category_id: 1,
      book: null
    };
  },
  apollo: {
    // Advanced query with parameters
    // The 'variables' method is watched by vue
    book: {
      query: book,
      // Reactive parameters
      variables() {
        if (this.$route && this.$route.params) {
          return {
            id: this.$route.params.id
          };
        }
      },
      // Optional result hook
      result({ data: { book } }) {
        this.title = book.title;
        this.author = book.author;
        this.image = book.image;
        this.description = book.description;
        this.link = book.link;
        this.featured = book.featured;
        this.category = book.category.id;
      }
    }
  },
  methods: {
    editBook() {
      this.$apollo
        .mutate({
          // Query
          mutation: updateBook,
          // Parameters
          variables: {
            id: this.$route.params.id,
            title: this.title,
            author: this.author,
            image: this.image,
            link: this.link,
            description: this.description,
            featured: this.featured,
            category_id: this.category_id
          }
        })
        .then(data => {
          // Result
          console.log(data);
          this.$router.push(`/book/${this.$route.params.id}`);
        })
        .catch(error => {
          // Error
          console.error(error);
        });
    }
  }
};
</script>
<style scoped>
.form-group {
  margin-bottom: 32px;
}
input[type="text"],
textarea {
  padding: 10px 14px;
  border: 1px solid lightgray;
  border-radius: 5px;
}
label {
  display: block;
}
button {
  padding: 16px;
  background: #027bff;
  color: white;
  border-radius: 5px;
  font-size: 16px;
}
</style>