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
      <v-form v-model="valid" class="formm"  @submit.prevent>
        <div class="grey lighten-2 pa-2 mb-4">
          <div class="file my-4 mb-6">
            <input
              type="file"
              id="file"
              class="d-none"
              multiple
              @change="selectFile"
            />
            <v-row>
              <v-col cols="12" sm="6" md="4" lg="3" xl="2">
                <v-btn @click="clickInput()" width="100%" type="button">Rasm tanlang</v-btn>
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
        <v-row>
          <v-col cols="12" sm="6">
            <div class="pa-2 grey lighten-2">
              <div class="mb-2">
                Kurs haqida <b>To'liqroq</b> ma'lumot kiriting
              </div>
              <div>
                <v-textarea
                  solo
                  name="input-7-4"
                  label="Matnni kiriting..."
                  v-model="kichik"
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
    props: ['ID'],
  data() {
    return {
      Textarea: [
        (v) => !!v || 'Matnni kiritish majburiy.',
      ],
      valid: false,
      title: '',
      kichik: '',
      image: [],
      t: false,
      error: '',
      data: []
    }
  },
  created() {
    this.$axios
      .get(`/cours/getAll/${this.ID}`)
      .then((res) => {
        const d = res.data[0]
        this.data = d
        this.title = d.title;
        this.kichik = d.description;
      })
      .catch((err) => {
        console.log(err)
      })
  },
  methods: {
    sendData() {
      if (this.image.length > 0 && this.image.length <= 10) {
        this.error = ''
        const data = {
          title: this.title,
          description: this.kichik,
        }

        this.$axios
          .put(`/cours/updateInfo/${this.ID}`, data)
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
          .put(`/cours/update/${this.ID}`, form)
          .then((res) => {
            console.log(res.data)
          })
          .catch((err) => {
            console.log(err)
          })

          this.$emit("Updated");
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
      if (event.target.files.length > 10) {
        this.error = 'Rasm tanlang'
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