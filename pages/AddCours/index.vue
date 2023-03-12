<template>
  <div class="pa-5">
    <div class="grey lighten-2 pa-3">
      <h2 class="mt-5 ms-4">Kurslar haqida ma'lumot qo'shing</h2>
      <v-form v-model="valid" @submit.prevent>
        <div class="my-8 col">
          <div class="file my-4 mb-6">
            <input
              type="file"
              id="file"
              multiple
              class="d-none"
              @change="selectFile"
            />
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
                <span class="me-3" v-for="(img, i) in image" :key="i">{{
                  img.name
                }}</span>
              </v-col>
            </v-row>
          </div>
          <div class="ps-1 pb-2 pt-5">Kurs nomi</div>
          <v-text-field
            solo
            v-model="title"
            :rules="Textarea"
            label="Kurs nomini kiriting..."
            required
          ></v-text-field>
        </div>
        <v-row class="px-3">
          <v-col cols="12" sm="12">
            <div class="ps-1 pb-2">
              Kurs haqida <b>to'liq</b> ma'lumot kiriting,
            </div>
            <v-textarea
              solo
              name="input-7-4"
              label="Matn kiriting..."
              v-model="kichik"
              :rules="Textarea"
            ></v-textarea>
          </v-col>

        </v-row>
        <v-row>
          <v-col cols="12" sm="6" lg="4" class="pt-0 pb-5 px-5">
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
      Textarea: [(v) => !!v || 'Matnni kiritish majburiy.'],
      valid: false,
      title: '',
      kichik: '',
      image: [],
      t: false,
      error: '',
    }
  },
  methods: {
    sendData() {
      if (this.image.length != 0 && this.image.length<=10) {
        this.error = ''
        let form = new FormData()

        form.append('title', this.title)
        form.append('description', this.kichik)

        for (let item of Object.keys(this.image)) {
          form.append('file', this.image[item])
        }

        this.$axios
          .post('/cours/add', form)
          .then((res) => {
            this.image = [];
            this.title = null;
            this.kichik = null;
            document.querySelector('#success').classList.add('success-active')
            setTimeout(() => {
              document
                .querySelector('#success')
                .classList.remove('success-active')
            }, 6000)
          })
          .catch((err) => {
            document.querySelector('#error').classList.add('error-active')
            setTimeout(() => {
              document.querySelector('#error').classList.remove('error-active')
            }, 6000)
          })
      } else {
        this.error = 'Rasm tanlang'
      }
    },
    clickInput() {
      document.querySelector('#file').click()
    },
    selectFile(event) {
      this.error = ''
      this.image = event.target.files
      if (event.target.files.length > 0) {
        this.t = true
      }
      if (event.target.files.length == 0 && event.target.files.length <=10) {
        this.error = 'Rasm tanlang'
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
