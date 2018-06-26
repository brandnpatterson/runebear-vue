<template>
  <div v-if="!loading" id="app">
    <Header :header="header" />
    <router-view/>
  </div>
</template>
<script>
import Header from './components/Header';
import { getTaxonomy, getPages, getWeeklyPosts } from './api'

export default {
  components: {
    Header
  },
  data() {
    return {
      authors: [],
      categories: null,
      pages: null,
      header: null,
      loading: true,
      footer: null,
      tags: null,
      weeklyPosts: null
    }
  },
  methods: {
    getRequests() {
      let allAuthors = [];
      let allCategories = [];
      let allTags = [];

      getPages()
        .then(data => {          
          this.pages = data.pages;
          this.header = data.header;
          this.footer = data.footer;
        });

      getWeeklyPosts()
        .then(weeklyPosts => {
          this.weeklyPosts = weeklyPosts;
        })
        .then(() => {
          const weeklyPosts = this.weeklyPosts;

          if (weeklyPosts.length === 0) {
            return;
          } else {
            return weeklyPosts.map(post => {
              const categories = [];
              const tagNames = [];

              getTaxonomy('tags', post.id)
                .then(data => {
                  if (data) {
                    return data.map(tag => {
                      tagNames.push(tag.name);
                      allTags.push(tag.name);
                      allTags = [...new Set(allTags)].sort();

                      return post.tagNames = tagNames;
                    })
                  } else {
                    return allTags.push('');
                  }
                })
                .then(() => {
                  this.tags = allTags;
                  this.weeklyPosts = weeklyPosts;
                });

              getTaxonomy('categories', post.id)
                .then(data => {
                  if (data) {
                    return data.map(tag => {
                      categories.push(tag.name);
                      allCategories.push(tag.name);
                      allCategories = [...new Set(allCategories)].sort();

                      return post.categories = categories;
                    })
                  } else {
                    return allCategories.push('');
                  }
                })
                .then(() => {
                  this.categories = allCategories;
                  this.weeklyPosts = weeklyPosts;
                })

              return getTaxonomy('post_author', post.id)
                .then(data => {
                  if (data && data[0] && data[0].name) {
                    const checkAndAdd = name => {
                      const found = allAuthors.some((el) => el.name === name);

                      if (!found) {
                        allAuthors.push({
                          name: data[0].name,
                          description: data[0].description,
                          slug: data[0].slug
                        });
                      }
                    }

                    checkAndAdd(data[0].name);

                    post.author = data[0].name;
                    post.authorSlug = data[0].slug;
                    post.authorDesc = data[0].description;
                  } else {
                    post.author = '';
                    post.authorSlug = '';
                    post.authorDesc = '';
                  }
                })
                .then(() => {
                  this.authors = allAuthors;
                  this.loading = false;
                  this.weeklyPosts = weeklyPosts;
                });
            });
          }
        });
    }
  },
  beforeMount(){
    this.getRequests();
  }
}
</script>
