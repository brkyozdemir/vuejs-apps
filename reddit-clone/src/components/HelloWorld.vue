
<template>
  <div class="hello">
    <ul class="list-unstyled">
      <li v-for="post in posts.slice(1)" v-bind:key="post.id" class="media m-3">
        <img :src="post.data.thumbnail" class="mr-3" alt="reddit-clone" />
        <div class="media-body">
          <!-- eslint-disable-next-line max-len -->
          <h5 class="mt-0 mb-1">
            <a :href="createLink(post.data.permalink)">{{post.data.title}}</a>
          </h5>
          <h3 class="text-danger">{{post.data.ups}} upvotes</h3>
          <p>created {{formatDate(post.data.created)}} ago by {{post.data.author}}</p>
          <p>
            <span class="badge badge-pill badge-secondary">
              {{post.data.num_comments}}
              comments
            </span>
          </p>
          <!-- eslint-disable-next-line max-len -->
          <button
            v-if="isImage(post)"
            @click="post.showImage = !post.showImage"
            type="button"
            class="btn btn-primary"
          >{{post.showImage ? 'Hide' : 'Show'}} Image</button>
          <div v-if="post.showImage">
            <img :src="post.data.url" alt="reddit-image" class="reddit-img" />
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import { parse } from 'date-fns/fp';
import formatDistance from 'date-fns/formatDistance';

export default {
  name: 'Posts',
  data() {
    return {
      posts: [],
    };
  },
  mounted() {
    this.load();
  },
  methods: {
    load() {
      const url = 'https://www.reddit.com/r/rarepuppers/.json';
      fetch(url)
        .then(response => response.json())
        .then((result) => {
          // eslint-disable-next-line no-console
          result.data.children.forEach((child) => {
            // eslint-disable-next-line no-param-reassign
            child.showImage = false;
          });
          this.posts = result.data.children;
        });
    },
    formatDate(date) {
      return formatDistance(new Date(date * 1000), new Date(2015, 0, 1));
    },
    createLink(url) {
      return `https://www.reddit.com${url}`;
    },
    isImage(post) {
      return post.data.url.match(/\.(jpg|png|jpeg|bpm)$/);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.reddit-img{
  width: 100%;
}
</style>
