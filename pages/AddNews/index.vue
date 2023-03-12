<template>
  <div class="pa-5">
    <div class="grey lighten-2 pa-3">
      <h2>Yangiliklar qo'shing</h2>
      <v-form v-model="valid" @submit.prevent>
        <div class="my-8 col">
          <div class="file my-4 mb-6">
            <input type="file" id="file" class="d-none" @change="selectFile" />
            <v-row>
              <v-col cols="12" sm="6" md="4" lg="3" xl="2">
                <v-btn @click="clickInput()" width="100%">Rasm tanlang</v-btn>
                <span class="red--text err">{{ error }}</span>
              </v-col>
              <v-col
                cols="12"
                sm="6"
                md="8"
                lg="9"
                xl="10"
                class="mt-1"
                v-if="t"
              >
                <span class="me-3" v-if="image != null">{{ image.name }}</span>
              </v-col>
            </v-row>
          </div>
          <v-text-field
            solo
            v-model="title"
            :rules="titleRules"
            label="Yangilik mavzusini kiriting"
            required
          ></v-text-field>
        </div>
        <v-row>
          <v-col cols="12" sm="12">
            <div class="col">
              <div>Yangilik haqida to'liqroq matn kiriting.</div>
              <div class="pt-5">
                <v-textarea
                  solo
                  name="input-7-4"
                  label="Matn kiriting..."
                  v-model="kichik"
                  :rules="titleRules"
                ></v-textarea>
              </div>
            </div>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="12">
            <div class="col">
              <div>Youtube dan video quyish quyish majburiy emas.</div>
              <div class="pt-5">
                <v-textarea
                  solo
                  name="input-7-4"
                  label="Youtubedan embed qilingan linkni kirgizing ..."
                  v-model="video"
                ></v-textarea>
              </div>
            </div>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" sm="6" lg="4">
            <v-btn
              @click="sendData()"
              width="100%"
              color="#1e871e"
              class="white--text"
              type="submit"
              :disabled="!valid"
              >Saqlash</v-btn
            >
          </v-col>
        </v-row>
      </v-form>
    </div>
    <div id="success" class="success white--text py-2 px-5">
      <v-icon color="white" size="18">mdi-check-bold</v-icon>
      <span>Ma'lumot qo'shildi.</span>
    </div>
    <div id="error" class="error white--text py-2 px-5">
      <v-icon color="white" size="18">mdi-alert-circle</v-icon>
      <span>Ma'lumot qo'shishda xatolik</span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      titleRules: [(v) => !!v || 'Matnni kiritish majburiy.'],
      valid: false,
      kichik: '',
      video: '',
      slug: '',
      image: null,
      t: false,
      error: '',
      datas: [],
    }
  },
  methods: {
    sendData() {
      if (this.image != null) {
        this.error = ''
        let lowStr = this.title.toLocaleLowerCase()
        this.slug = lowStr.replaceAll(' ', '-')
        let form = new FormData()

        form.append('name', this.title)
        form.append('description', this.kichik)
        form.append('slug', this.slug)
        form.append('video', this.video)
        form.append('image', this.image, this.image.name)

        this.$axios
          .post('/news/add', form)
          .then((res) => {
            document.querySelector('#success').classList.add('success-active')
            setTimeout(() => {
              document
                .querySelector('#success')
                .classList.remove('success-active')
            }, 6000)
            this.image = null
            this.title = ''
            this.kichik = ''
          })
          .catch((err) => {
            document.querySelector('#error').classList.add('error-active')
            setTimeout(() => {
              document.querySelector('#error').classList.remove('error-active')
            }, 6000)
          })
      } else {
        this.error = '1 ta rasm tanlang'
      }
    },
    clickInput() {
      document.querySelector('#file').click()
    },
    selectFile(event) {
      this.error = ''
      this.image = event.target.files[0]
      if (event.target.files.length > 0) {
        this.t = true
      }
      if (event.target.files.length != 1) {
        this.error = '1 ta rasm tanlang'
      }
    },
  },
}
</script>

<style scoped>
.err {
  font-size: 12px;
}
.success {
  position: fixed;
  z-index: 20;
  top: 100px;
  right: -100%;
  transition: 1s ease all;
}
.success-active {
  right: 20px;
}
.error {
  position: fixed;
  z-index: 20;
  top: 100px;
  right: -100%;
  transition: 1s ease all;
}
.error-active {
  right: 20px;
}
</style>
