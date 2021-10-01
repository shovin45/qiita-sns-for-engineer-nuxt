<template>
  <div>
    <v-textarea v-model="text" solo name="input-7-4" label="つぶやく" />
    <v-btn @click="tweeee(text)">つぶやく</v-btn>

    <v-row class="mt-8">
      <v-col v-for="post in posts" :key="post.id" cols="12">
        <v-card>
          <v-list-item class="grow">
            <v-list-item-content>
              <v-list-item-title>{{
                getUserName(post._user_id)
              }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-card-text class="font-weight-bold">
            {{ post.text }}
          </v-card-text>

          <v-card-text>
            {{ new Date(post._created_at) }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  async asyncData({ $axios }) {
    const res = await $axios.get(
      '/text/all?$orderby=_created_at+desc&$limit=20'
    )

    return { posts: res.data }
  },
  data() {
    return {
      text: '',
      users: [],
      loaded: false,
    }
  },
  computed: {
    getUserName() {
      return (id) => {
        for (const user of this.users) {
          if (id === user.id) return user.name
        }
        return 'ユーザ無し'
      }
    },
  },
  mounted() {
    for (const post of this.posts) {
      this.getUser(post._user_id)
    }
  },

  methods: {
    tweeee(text) {
      this.$axios.post('/text', { text })
    },
    getTweet() {
      this.$axios
        .get('/text/all?$orderby=_created_at+desc&$limit=20')
        .then((res) => {
          this.posts.push(...res.data)
        })
    },
    getUser(id) {
      this.$axios.get(`/user/${id}`).then((res) => {
        this.users.push(res.data)
      })
    },
  },
}
</script>
