<template>
  <div>
    <b-container>
      <!-- 1 -->
      <div>
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
      </div>
      <!-- 8 -->
      <div>
        <b-form-file
          v-model="file"
          :state="Boolean(file)"
          placeholder="Choose a file or drop it here..."
          drop-placeholder="Drop file here..."
        />
        <div class="mt-3">
          Selected file: {{ file ? file.name : '' }}
        </div>
        <b-button class="" @click="fileUpload">
          Upload
        </b-button>
      </div>
      <!-- 3 -->
      <div>
        <p v-if="$fetchState.pending">
          Fetching samples...
        </p>
        <p v-else-if="$fetchState.error">
          An error occurred :(
        </p>
        <div v-else>
          <h1>samples</h1>
          <b-table
            striped
            hover
            sticky-header
            :items="csv_values"
            :fields="headers"
          />
        </div>
        <button @click="$fetch">
          Refresh
        </button>
      </div>
    </b-container>
  </div>
</template>

<script>
export default {
  data () {
    return {
      sentence: '',
      words: [],
      fileupload: false,
      filename: '',
      file: null,
      csv_get: {},
      samples: [],
      headers: [],
      csv_values: []
    }
  },
  async fetch () {
    console.log('Start fetch')
    this.csv_get = await fetch(
      'http://0.0.0.0:4000/aialgo7/'
    ).then(res => res.json())
    // csv_data(配列)を取得
    const csvData = this.csv_get.body.csv_data
    // ヘッダーとそれぞれの要素に分ける
    this.headers = csvData.slice(0, 1)
    console.log('this.headers: ', this.headers)
    this.csv_values = csvData.slice(1, -1)
    console.log('this.csv_values: ', this.csv_values)
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
    },
    async fileUpload () {
      console.log('Call fileUpload')

      const file = this.file
      /* Make sure file exists */
      console.log('file: ', file)
      if (!file) { return }
      /* create a reader */
      const readData = f => new Promise((resolve) => {
        const reader = new FileReader()
        reader.readAsDataURL(f)
        reader.onloadend = () => resolve(reader.result.replace('data:', '').replace(/^.+,/, ''))
      })
      /* Read data */
      const data = await readData(file)
      console.log('data: ', data)
      console.log('data_type: ', typeof data)

      const filename = file.name
      console.log('filename: ', filename)

      const config = {
        headers: { 'content-type': 'application/json' }
      }
      const params = {
        filename,
        data
      }
      const url = 'http://0.0.0.0:4000/aialgo3/'
      try {
        // 引数の順番は変更不可
        this.fileupload = await this.$axios.$post(
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
