<template>
  <div id="modal">
    <div class="form white pa-10 grey lighten-2">
      <v-btn
        type="button"
        class="error close py-3 mt-10 me-10"
        @click="$emit('Close')"
      >
        <v-icon>mdi-close</v-icon>
      </v-btn>
      <v-form v-model="valid" @submit.prevent class="formm">
        <div class="my-2 col">
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
                  solo
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
                  solo
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
  </div>
</template>

<script>
export default {
  data() {
    return {
      Textarea: [
        (v) => !!v || 'Matnni kiritish majburiy.',
        (v) => v.length <= 200 || 'Kiritgan matningiz 200 ta belgidan oshmasin',
      ],
      valid: false,
      studetRules: [
        (v) => !!v || "Ma'lumot to'ldirish majburiy",
      ],
      textarea: '',
      studentSoni: '',
      studentMatn: '',
      sovrinlarSoni: '',
      sovrinMatn: '',
      image: [],
      t: false,
      error: '',
      id: '',
    }
  },
  created() {
    this.$axios
      .get('/about/getAll')
      .then((res) => {
        const data = res.data[0]
        this.textarea = data.description
        this.studentSoni = Number(data.student)
        this.studentMatn = data.studentTitle
        this.sovrinlarSoni = Number(data.prize)
        this.sovrinMatn = data.prizeTitle
        this.id = data._id
      })
      .catch((err) => {
        console.log(err)
      })
  },
  methods: {
    sendData() {
      if (this.image.length == 2) {
        this.error = ''
        const data = {
          description: this.textarea,
          student: Number(this.studentSoni),
          studentTitle: this.studentMatn,
          prize: Number(this.sovrinlarSoni),
          prizeTitle: this.sovrinMatn,
        }
        console.log(data)

        this.$axios
          .put(`/about/updateInfo/${this.id}`, data)
          .then((res) => {
            console.log(res.data)
          })
          .catch((err) => {
            console.log(err)
          })

        let form = new FormData()

        for (let item of Object.keys(this.image)) {
          form.append('file', this.image[item])
        }

        this.$axios
          .put(`/about/update/${this.id}`, form)
          .then((res) => {
            console.log(res.data)
          })
          .catch((err) => {
            console.log(err)
          })

          this.$emit("Updated");
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
#modal {
  width: 100%;
  height: 100%;
  position: fixed;
  background: rgba(0, 0, 0, 0.432);
  top: 0;
  left: 0;
  z-index: 200;
  padding: 50px;
}
#modal .form {
  position: relative;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
}
.formm {
  width: 100%;
  height: 100%;
  position: relative;
  top: 0;
  left: 0;
  overflow-y: scroll;
  overflow-x: hidden;
}
::-webkit-scrollbar {
  display: none;
}
.close {
  position: absolute;
  top: 0;
  right: 0;
  z-index: 20;
}
@media (max-width: 500px) {
  #modal {
    padding: 0;
  }
  .form {
    padding: 20px !important;
  }
  .close {
    top: -30px !important;
    right: -30px !important;
  }
}
</style>