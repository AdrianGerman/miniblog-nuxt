<template>
  <div class="box">
    <article class="post">
      <h1>{{ post.title }}</h1>
      <div class="author">
        <p>Por {{ post.author }}</p>
        <small>Fecha de publicación: {{ post.updated }}</small>
      </div>
      <p>{{ post.description }}</p>
      <figure>
        <img :src="post.cover" :alt="post.cover" />
        <figcaption>Portada - {{ post.title }}</figcaption>
      </figure>
      <VueMarkdown class="markdown">{{ post.content }}</VueMarkdown>
      <div ref="comments" class="comments">
        <h3 class="title">Comentarios</h3>
        <p class="total-comments">
          Hay {{ article['total-comments'] || 0 }} comentarios
        </p>
        <div class="comments-list">
          <CommentItem v-for="comment in comments" :key="comment._id" v-bind="comment" />
        </div>
        <div class="add-comment">
          <InputComment @submit="createComment" />
        </div>
      </div>
    </article>
  </div>
</template>

<script>
import VueMarkdown from 'vue-markdown'
export default {
  name: 'ArticlePage',
  components: {
    VueMarkdown,
  },
  asyncData({ params, $http, isDev }) {
    const { slug } = params
    const url = isDev ? 'http://localhost:9999' : 'https://miniblog-nuxt2.netlify.app';
    const article = $http.$get(`${url}/.netlify/functions/article?slug=${slug}`)
    return article
  },
  // data() {
  //   return {
  //     post: {
  //       slug: 'mi-primer-post',
  //       title: 'Mi primer post',
  //       author: 'Adrian German',
  //       updated: '30/05/2024',
  //       description: 'lorem ispum dolor sit amet',
  //       cover: 'https://via.placeholder.com/1024x420/',
  //       content: '# Title\n\n## Second title\n\nLorem ipsum dolor sit amet',
  //     },
  //   }
  // },
  //meta tags
  head() {
    return {
      title: this.post?.title,
      meta: [{ name: 'description', content: this.post?.description || '' }]
    }
  },
  computed: {
    post() {
      return {
        title: this.article?.title,
        author: this.article['author-name'][0],
        updated: new Date(this.article?.updated).toLocaleDateString(),
        description: this.article?.description,
        cover: this.article?.cover[0].thumbnails.full.url,
        content: this.article?.content
      }
    }
  },
  methods: {
    async createComment(comment) {
      this.$nuxt.$loading.start();
      const url =
        location.hostname === 'localhost'
          ? 'http://localhost:9999'
          : 'https://miniblog-nuxt2.netlify.app'
      await fetch(`${url}/.netlify/functions/comment?article=${this.article._id}`,
        { method: 'post', body: JSON.stringify(comment) }
      )
      this.$nuxt.refresh();
      this.$nuxt.$loading.finish();
    },
  },
}
</script>


<style lang="scss">
.box {
  @apply min-h-full;
}

article.not-found {
  @apply flex flex-col justify-center items-center gap-4 p-16;

  h1 {
    @apply text-2xl font-bold;
  }

  .nuxt-link-active {
    @apply underline;
  }
}

article.post {
  @apply m-auto p-4 w-full max-w-3xl;

  >h1 {
    @apply font-black text-4xl mt-6 my-4;
  }

  >div.author {
    @apply text-gray-800;

    small {
      @apply italic;
    }
  }

  >p {
    @apply block text-gray-700 font-semibold my-4;
  }

  >figure {
    @apply w-full my-4;

    img {
      @apply w-full;
    }

    figcaption {
      @apply italic text-gray-400 text-right;
    }
  }

  .markdown {

    h1,
    h2,
    h3,
    h4,
    h5,
    h6 {
      @apply font-bold my-6;
    }

    h1 {
      @apply text-3xl;
    }

    h2 {
      @apply text-2xl;
    }

    h3 {
      @apply text-xl;
    }

    h4 {
      @apply text-lg;
    }

    p {
      @apply font-serif inline-block w-full my-4;
    }

    a {
      @apply underline;
    }

    pre {
      @apply w-full block bg-gray-200 font-mono my-4 px-4 py-4 box-border overflow-x-auto;
    }

    code {
      @apply inline bg-gray-200 font-mono px-1;
    }

    em {
      @apply italic;
    }

    strong {
      @apply font-bold;
    }

    blockquote {
      @apply block w-full my-4 px-4 py-2 bg-gray-200 border-l-8 border-gray-600;
    }

    ol {
      @apply ml-8 list-disc;
    }

    ul {
      @apply ml-8 list-decimal;
    }

    hr {
      @apply my-8;
    }

    dl {
      @apply bg-gray-100 my-4 px-6 pt-3 pb-4;

      dt {
        @apply font-bold font-sans;
      }

      dd {
        @apply italic text-sm text-gray-800 ml-8 mb-1;
      }
    }

    table {
      @apply block table-auto my-4 text-left whitespace-nowrap overflow-x-auto;

      thead {
        @apply w-full bg-gray-300;

        th {
          @apply border border-collapse px-3 py-2;
        }
      }

      tbody {
        @apply w-full;

        tr {
          td {
            @apply border border-collapse px-3 py-2;
          }
        }

        tr:nth-child(even) {
          @apply bg-gray-100;
        }
      }
    }
  }

  .comments {
    @apply block w-full bg-gray-200 my-4 px-6 py-6;

    .title {
      @apply font-bold text-2xl;
    }

    .total-comments {
      @apply mb-4;
    }

    .comments-list {
      @apply flex flex-col gap-4;
    }
  }

  .to-top {
    @apply pointer-events-none absolute sticky w-full flex justify-end my-4 px-4 py-4 left-0 right-0 bottom-0;

    button {
      @apply pointer-events-auto border rounded-lg px-6 py-2 bg-white;
    }
  }
}
</style>
