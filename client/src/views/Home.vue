<template>
  <div class="home">
    <router-link to="/books/add">AddBook</router-link>
    <ApolloQuery :query="categoriesQuery">
      <template slot-scope="{ result: { data, loading }, isLoading }">
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <a href="#" class="link-margin" @click.prevent="selectCategory('all')"
            >All</a
          >
          <a
            href="#"
            class="link-margin"
            @click.prevent="selectCategory('featured')"
            >Featured</a
          >
          <a
            href="#"
            v-for="category of data.categories"
            :key="category.id"
            @click.prevent="selectCategory(category.id)"
            class="link-margin"
            >{{ category.id }} {{ category.name }}</a
          >
        </div>
      </template>
    </ApolloQuery>

    <div v-if="selectedCategory === 'all'">
      <ApolloQuery :query="query">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <router-link
              :to="`/book/${book.id}`"
              v-for="book of data.books"
              :key="book.id"
              class="link-margin"
            >
              <div>
                {{ book.id }}.{{ book.title }}
                <div>
                  <img
                    :src="`http://127.0.0.1:8000/img/${book.image}`"
                    alt="Cover Image"
                  />
                </div>
              </div>
            </router-link>
          </div>
        </template>
      </ApolloQuery>
    </div>

    <div v-else-if="selectedCategory === 'featured'">
      <ApolloQuery :query="query" :variables="{ featured: true }">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div>
              <router-link
                :to="`/book/${book.id}`"
                v-for="book of data.featured"
                :key="book.id"
                class="link-margin"
              >
                <div>
                  {{ book.id }}.{{ book.title }}
                  <div>
                    <img :src="`http://127.0.0.1:8000/img/${book.image}`" alt />
                  </div>
                </div>
              </router-link>
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>

    <div v-else>
      <ApolloQuery :query="categoryQuery" :variables="{ id: selectedCategory }">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div>
              <router-link
                :to="`/book/${book.id}`"
                v-for="book of data.category.books"
                :key="book.id"
                class="link-margin"
              >
                <div>
                  {{ book.id }}.{{ book.title }}
                  <div>
                    <img :src="`http://127.0.0.1:8000/img/${book.image}`" alt />
                  </div>
                </div>
              </router-link>
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>
  </div>
</template>

<script>
import categoryQuery from '@/graphql/queries/Category.gql';
import categoriesQuery from '@/graphql/queries/Categories.gql';
import booksQuery from '@/graphql/queries/Books.gql';
import booksFeaturedQuery from '@/graphql/queries/BooksFeatured.gql';
export default {
  name: 'Home',
  components: {},
  data() {
    return {
      categoryQuery,
      categoriesQuery,
      booksQuery,
      booksFeaturedQuery,
      selectedCategory: 'all',
      query: booksQuery,
      categories: []
    };
  },
  methods: {
    selectCategory(category) {
      if (category === 'all') {
        this.query = booksQuery;
      } else if (category === 'featured') {
        this.query = booksFeaturedQuery;
      } else {
        this.query = categoryQuery;
      }
      this.selectedCategory = category;
    }
  }
};
</script>
<style>
.link-margin {
  margin-right: 25px;
}
</style>
