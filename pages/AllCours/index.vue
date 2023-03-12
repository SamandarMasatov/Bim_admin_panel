<template>
  <div>
    <div class="pa-5">
      <div v-if="datas.length == 0">
        <h3 class="mt-5">Kurslar yo'q</h3>
        <v-btn class="info mt-3" to="/AddCours">Kurs qo'shish</v-btn>
      </div>
      <div
        class="data grey lighten-3 mt-5 mb-8 pa-5"
        v-for="(data, i) in datas"
        :key="i"
      >
        <div>
          <v-btn
            class="error me-5 body-2"
            @click="
              modal = true
              id = data._id
            "
            >O'chirish</v-btn
          >
          <v-btn
            class="success body-2"
            @click="
              addModal = true
              id = data._id
            "
            >Yangilash</v-btn
          >
        </div>
        <v-row class="my-5">
          <v-col cols="12" md="6">
            <v-carousel hide-delimiters>
              <v-carousel-item
                v-for="(item, i) in data.file"
                :key="i"
                :src="`http://localhost:3030/uploads/cours/${item}`"
              ></v-carousel-item>
            </v-carousel>
          </v-col>
          <v-col cols="12" md="6">
            <v-row>
              <v-col cols="12">
                <v-icon size="20">mdi-calendar-month-outline</v-icon>
                <span class="ms-3"
                  >{{ data.date[0] }}{{ data.date[1] }}{{ data.date[2]
                  }}{{ data.date[3] }}/{{ data.date[5] }}{{ data.date[6] }}/{{
                    data.date[8]
                  }}{{ data.date[9] }}
                </span>
              </v-col>
              <v-col cols="12" class="py-0">
                <b>
                  {{ data.title }}
                </b>
              </v-col>
              <v-col cols="12">
                {{ data.description }}
              </v-col>
            </v-row>
            <!-- Ma'lumotlar -->
          </v-col>
        </v-row>
      </div>
      <div class="modal" :class="modal ? 'modal-active' : ''">
        <div class="modal-content">
          <div class="modal-header mb-5 text-center">
            <b>Ishonchingiz komilmi ?</b>
          </div>
          <div class="modal-footer">
            <v-btn class="error me-5" @click="remove(id)">O'chirish</v-btn>
            <v-speacer></v-speacer>
            <v-btn @click="modal = false" class="success">Bekor qilish</v-btn>
          </div>
        </div>
      </div>
      <div id="success" class="successs success white--text py-2 px-5">
        <v-icon color="white" size="18">mdi-check-bold</v-icon>
        <span>{{ success }}</span>
      </div>
      <div id="error" class="errorr error white--text py-2 px-5">
        <v-icon color="white" size="18">mdi-alert-circle</v-icon>
        <span>Ma'lumot qo'shishda xatolik</span>
      </div>
    </div>
    <UpdateCours
      v-if="addModal"
      :ID="id"
      @Close="close()"
      @Updated="updated()"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      datas: [],
      modal: false,
      id: '',
      addModal: false,
      success: '',
    }
  },
  created() {
    this.getAll()
  },
  methods: {
    updated() {
      let a = setInterval(() => {
        this.getAll()
      }, 100)
      setTimeout(() => {
        clearInterval(a)
      }, 200)
      this.addModal = false
      this.success = "Ma'lumot yangilandi"
      document.querySelector('#success').classList.add('success-active')
      setTimeout(() => {
        document.querySelector('#success').classList.remove('success-active')
      }, 6000)
    },
    close() {
      this.addModal = false
    },
    getAll() {
      this.$axios
        .get('/cours/getAll')
        .then((res) => {
          this.datas = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    remove(id) {
      this.modal = false
      this.$axios
        .delete(`/cours/delete/${id}`)
        .then((res) => {
          this.success = "Ma'lumot o'chirildi"
          document.querySelector('#success').classList.add('success-active')
          setTimeout(() => {
            document
              .querySelector('#success')
              .classList.remove('success-active')
          }, 6000)
          this.getAll()
        })
        .catch((err) => {
          console.log(err)
          document.querySelector('#error').classList.add('error-active')
          setTimeout(() => {
            document.querySelector('#error').classList.remove('error-active')
          }, 6000)
        })
    },
  },
}
</script>

<style scoped>
.relative {
  position: relative;
}
.absolute {
  position: absolute;
}
.modal {
  position: fixed;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.342);
  top: 0;
  left: 0;
  z-index: 20;
  display: none;
  align-items: center;
  justify-content: center;
  transition: 0.5s ease all;
}
.modal .modal-content {
  background: white;
  padding: 20px;
  border-radius: 5px;
}
.modal-active {
  display: flex;
}
.successs {
  position: fixed;
  z-index: 20;
  top: 100px;
  right: -100%;
  transition: 1s ease all;
}
.success-active {
  right: 20px;
}
.errorr {
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