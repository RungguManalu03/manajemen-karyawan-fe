<template>
  <card>
    <h4 slot="header" class="card-title">Add User</h4>
    <form @submit.prevent="addUser">
      <div class="row">
        <div class="col-md-6">
          <div class="col-md-12">
            <base-input v-model="newUser.nama" type="text" label="Nama Karyawan" required :error="errors.nama">
            </base-input>
          </div>
          <div class="col-md-12">
            <base-input v-model="newUser.nomor" type="text" label="Nomor Karyawan" required :error="errors.nomor">
            </base-input>
          </div>
          <div class="col-md-12">
            <label>Jabatan</label>
            <select v-model="newUser.jabatan" class="form-control" required>
              <option value="">Select Jabatan</option>
              <option v-for="option in jabatanOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.jabatan">{{ errors.jabatan }}</small>
          </div>
          <div class="col-md-12">
            <label>Departemen</label>
            <select v-model="newUser.departemen" class="form-control" required>
              <option value="">Select Departemen</option>
              <option v-for="option in departemenOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.departemen">{{ errors.departemen }}</small>
          </div>
          <div class="col-md-12">
            <label>Status</label>
            <select v-model="newUser.status" class="form-control" required>
              <option value="">Select Status</option>
              <option v-for="option in statusOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.status">{{ errors.status }}</small>
          </div>
          <div class="col-md-12">
            <base-input v-model="newUser.tanggal_masuk" type="date" label="Tanggal Masuk" required
              :error="errors.tanggal_masuk">
            </base-input>
          </div>
        </div>
        <div class="col-md-6">
          <div class="col-md-12">
            <base-input type="file" label="Foto" @change="handleFileUpload" accept="image/*"
              :error="errors.foto">
            </base-input>
          </div>
        </div>
      </div>
      <div class="text-center">
        <button type="submit" class="btn btn-info btn-fill float-right">
          Add User
        </button>
      </div>
      <div class="clearfix"></div>
    </form>
  </card>
</template>

<script>
import Card from 'src/components/Cards/Card.vue'
import axios from 'axios'

export default {
  components: {
    Card
  },
  data() {
    return {
      newUser: {
        nama: '',
        nomor: '',
        jabatan: '',
        departemen: '',
        status: '',
        tanggal_masuk: '',
        foto: null
      },
      errors: {},
      jabatanOptions: [
        'Social Media Manager',
        'UI/UX Designer',
        'Backend Developer',
        'HR Generalist'
      ],
      departemenOptions: [
        'Tech',
        'HR',
        'Finance',
        'Marketing'
      ],
      statusOptions: [
        'kontrak',
        'probation',
        'tetap'
      ]
    }
  },
  methods: {
    handleFileUpload(event) {
      this.newUser.foto = event.target.files[0]
    },
    validateForm() {
      this.errors = {}
      if (!this.newUser.nama) this.errors.nama = 'Nama is required'
      if (!this.newUser.nomor) this.errors.nomor = 'Nomor is required'
      if (!this.newUser.jabatan) this.errors.jabatan = 'Jabatan is required'
      if (!this.newUser.departemen) this.errors.departemen = 'Departemen is required'
      if (!this.newUser.status) this.errors.status = 'Status is required'
      if (!this.newUser.tanggal_masuk) this.errors.tanggal_masuk = 'Tanggal Masuk is required'
      if (!this.newUser.foto) this.errors.foto = 'Foto is required'
      return Object.keys(this.errors).length === 0
    },
    async addUser() {
      if (!this.validateForm()) return

      try {
        const formData = new FormData()
        for (const key in this.newUser) {
          formData.append(key, this.newUser[key])
        }

        const response = await axios.post('http://localhost:3000/karyawan/api/v1', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })

        alert("berhasil menambah karyawan")
        window.location.href = 'http://localhost:8080/#/admin/table-list';
        console.log('User added successfully:', response.data)
        this.resetForm()
      } catch (error) {
        console.error('Error adding user:', error)
      }
    },
    resetForm() {
      this.newUser = {
        nama: '',
        nomor: '',
        jabatan: '',
        departemen: '',
        status: '',
        tanggal_masuk: '',
        foto: null
      }
      this.errors = {}
    }
  }
}
</script>

<style scoped>
.col-md-12 {
  margin-bottom: 15px;
}

select.form-control {
  height: 40px;
}
</style>
