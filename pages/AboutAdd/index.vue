<template>
  <div class="pa-5">
    <div class="grey lighten-2 pa-3" v-if="datas.length < 1">
      <h2>Biz haqimizda ma'lumot qo'shing</h2>
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
          <v-textarea
            solo
            name="input-7-4"
            label="San'atni chuqurroq o'rganish haqida qisqacha matn kiriting."
            v-model="textarea"
            :rules="Textarea"
          ></v-textarea>
        </div>
        <v-row>
          <v-col cols="12" sm="6">
            <div class="col">
              <div>
                <v-text-field
                  clear-icon
                  v-model="studentSoni"
                  type="number"
                  :rules="studetRules"
                  :counter="10"
                  label="Barcha studentlar"
                  required
                ></v-text-field>
              </div>
              <div class="pt-5">
                <v-textarea
                  solo
                  name="input-7-4"
                  label="Studentlar haqida ma'lumot kiriting"
                  v-model="studentMatn"
                  :rules="Textarea"
                ></v-textarea>
              </div>
            </div>
          </v-col>
          <v-col cols="12" sm="6">
            <div class="col">
              <div>
                <v-text-field
                  v-model="sovrinlarSoni"
                  :rules="studetRules"
                  type="number"
                  :counter="10"
                  label="Barcha sovrinlar"
                  required
                ></v-text-field>
              </div>
              <div class="pt-5">
                <v-textarea
                  solo
                  name="input-7-4"
                  label="Sovrinlar haqida ma'lumot kiriting"
                  v-model="sovrinMatn"
                  :rules="Textarea"
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
    <div v-else>
      <h2 class="mt-5">Biz haqimizda bo'limiga ma'lumot qo'shilgan.</h2>
      <v-btn class="info mt-5" to="/AllAbout">ko'rish</v-btn>
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
      Textarea: [
        (v) => !!v || 'Matnni kiritish majburiy.',
        // (v) => v.length <= 200 || 'Kiritgan matningiz 200 ta belgidan oshmasin',
      ],
      valid: false,
      textarea: '',
      studentSoni: '',
      studentMatn: '',
      sovrinlarSoni: '',
      sovrinMatn: '',
      studetRules: [
        (v) => !!v || "Ma'lumot to'ldirish majburiy",
        (v) => v.length <= 10 || 'Name must be less than 10 characters',
      ],
      image: [],
      t: false,
      error: '',
      datas: [],
    }
  },
  created() {
    this.getAll()
  },
  methods: {
    getAll() {
      this.$axios
        .get('/about/getAll')
        .then((res) => {
          this.datas = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    sendData() {
      if (this.image.length == 2) {
        this.error = ''
        let form = new FormData()

        form.append('description', this.textarea)
        form.append('student', this.studentSoni)
        form.append('studentTitle', this.studentMatn)
        form.append('prize', this.sovrinlarSoni)
        form.append('prizeTitle', this.sovrinMatn)

        for (let item of Object.keys(this.image)) {
          form.append('file', this.image[item])
        }

        this.$axios
          .post('/about/add', form)
          .then((res) => {
            document.querySelector('#success').classList.add('success-active')
            setTimeout(() => {
              document
                .querySelector('#success')
                .classList.remove('success-active')
            }, 6000)
            this.getAll()
          })
          .catch((err) => {
            document.querySelector('#error').classList.add('error-active')
            setTimeout(() => {
              document.querySelector('#error').classList.remove('error-active')
            }, 6000)
          })
      } else {
        this.error = '2 ta rasm tanlang'
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
      if (event.target.files.length != 2) {
        this.error = '2 ta rasm tanlang'
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
