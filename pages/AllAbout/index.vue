<template>
  <div>
    <div v-if="datas.length > 0" class="pa-5">
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
          <v-btn class="success body-2" @click="addModal = true"
            >Yangilash</v-btn
          >
        </div>
        <v-row class="my-5">
          <v-col cols="12" md="5">
            <div class="images relative">
              <v-img
                width="100%"
                height="400"
                :src="`http://localhost:3030/uploads/about/${data.file[0]}`"
              />
              <div class="absolute img-2 pt-3 ps-3">
                <v-img
                  width="180"
                  height="180"
                  :src="`http://localhost:3030/uploads/about/${data.file[1]}`"
                ></v-img>
              </div>
            </div>
          </v-col>
          <v-col cols="12" md="7">
            <v-row class="mt-8">
              <v-col cols="12">
                {{ data.description }}
              </v-col>
            </v-row>
            <v-row class="my-8">
              <v-col cols="12" sm="3" class="text-center"
                ><h2 class="d-inline-block">{{ data.student }}</h2>
                <br />
                <b>Barcha studentlar</b></v-col
              >
              <v-col cols="12" sm="9" class="border px-5">{{
                data.studentTitle
              }}</v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="3" class="text-center"
                ><h2 class="d-inline-block">{{ data.prize }}</h2>
                <br />
                <b>Barcha sovrinlar</b></v-col
              >
              <v-col cols="12" sm="9" class="border px-5">{{
                data.prizeTitle
              }}</v-col>
            </v-row>
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
    <div class="pa-5" v-else>
      <h2 class="my-5">Biz haqimizda bo'limiga ma'lumot yo'q</h2>
      <v-btn class="info" to="/AboutAdd">Ma'lumot qo'shish</v-btn>
    </div>
    <UpdateAbout v-if="addModal" @Close="close()" @Updated="updated()" />
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
      this.addModal = false
      this.success = "Ma'lumot yangilandi"
      this.$axios
        .get('/about/getAll')
        .then((res) => {
          this.datas = res.data
        })
        .catch((err) => {
          console.log(err)
        })
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
        .get('/about/getAll')
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
        .delete(`/about/delete/${id}`)
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
.border {
  border-left: 3px solid grey;
}
.img-2 {
  bottom: 0;
  right: 0;
  background: #eeeeee;
}
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