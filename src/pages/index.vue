<template>
  <div>
    <b-container>
      <h1 class="mt-5">
        キーワードを入力してください。
      </h1>
      <b-input-group>
        <b-form-input v-model="sentence" type="text" />
        <b-button @click="getWords">
          Button
        </b-button>
      </b-input-group>
      <p>出力結果</p>
      <div v-if="!words" />
      <div v-else>
        <b-list-group>
          <b-list-group-item v-for="(word, key) in words" :key="key">
            {{ word.word }}
          </b-list-group-item>
        </b-list-group>
      </div>
    </b-container>
  </div>
</template>

<script>
export default {
  data () {
    return {
      sentence: '',
      words: []
    }
  },
  methods: {
    async getWords () {
      console.log('Call getWords')
      const sentence = this.sentence
      const config = {
        headers: { 'content-type': 'application/json' }
      }
      const params = {
        sentence
      }
      const url = 'http://0.0.0.0:4000/aialgo8/'
      try {
        // 引数の順番は変更不可
        this.words = await this.$axios.$post(
          url,
          params,
          config
        )
        console.log('SUCCESS')
      } catch (e) {
        console.log('ERROR')
        console.log(e)
      }
    }
  }
}
</script>
