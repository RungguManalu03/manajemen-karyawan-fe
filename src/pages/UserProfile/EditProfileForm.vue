<template>
  <card>
    <h4 slot="header" class="card-title">User Details / Edit</h4>
    <form @submit.prevent="updateUser">
      <div class="row">
        <div class="col-md-6">
          <div class="col-md-12">
            <base-input v-model="user.nama" type="text" label="Nama Karyawan" required :error="errors.nama">
            </base-input>
          </div>
          <div class="col-md-12">
            <base-input v-model="user.nomor" type="text" label="Nomor Karyawan" required :error="errors.nomor">
            </base-input>
          </div>
          <div class="col-md-12">
            <label>Jabatan</label>
            <select v-model="user.jabatan" class="form-control" required>
              <option value="">Select Jabatan</option>
              <option v-for="option in jabatanOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.jabatan">{{ errors.jabatan }}</small>
          </div>
          <div class="col-md-12">
            <label>Departemen</label>
            <select v-model="user.departemen" class="form-control" required>
              <option value="">Select Departemen</option>
              <option v-for="option in departemenOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.departemen">{{ errors.departemen }}</small>
          </div>
          <div class="col-md-12">
            <label>Status</label>
            <select v-model="user.status" class="form-control" required>
              <option value="">Select Status</option>
              <option v-for="option in statusOptions" :key="option" :value="option">{{ option }}</option>
            </select>
            <small class="text-danger" v-if="errors.status">{{ errors.status }}</small>
          </div>
          <div class="col-md-12">
            <base-input v-model="user.tanggal_masuk" type="date" label="Tanggal Masuk" required
              :error="errors.tanggal_masuk">
            </base-input>
          </div>
        </div>
        <div class="col-md-6">
          <div class="col-md-12">
            <base-input type="file" label="Foto" @change="handleFileUpload" accept="image/*" :error="errors.foto">
            </base-input>
          </div>
          <div class="col-md-12 mt-3">
            <img v-if="user.foto" :src="getFotoUrl(user.foto)" alt="User Photo" class="img-thumbnail"
              style="max-width: 200px;">
          </div>
        </div>
      </div>
      <div class="text-center">
        <button type="submit" class="btn btn-info btn-fill float-right">
          Update User
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
      user: {
        id: '',
        nama: '',
        nomor: '',
        jabatan: '',
        departemen: '',
        status: '',
        tanggal_masuk: '',
        foto: null
      },
      newFoto: null,
      errors: {},
      jabatanOptions: [
        'Social Media Manager',
        'UI/UX Designer',
        'Backend Developer',
        'HR Generalist',
        'STAFF'
      ],
      departemenOptions: [
        'Tech',
        'HR',
        'Finance',
        'Marketing',
        'IT'
      ],
      statusOptions: [
        'kontrak',
        'probation',
        'tetap'
      ]
    }
  },
  methods: {
    async fetchUserDetails() {
      try {
        const userId = this.$route.params.id;
        const response = await axios.get(`http://localhost:3000/karyawan/api/v1/${userId}`);
        this.user = response.data.data[0];
        this.user.tanggal_masuk = this.formatDateForInput(this.user.tanggal_masuk);
      } catch (error) {
        console.error('Error fetching user details:', error);
      }
    },
    handleFileUpload(event) {
      this.newFoto = event.target.files[0]
    },
    getFotoUrl(foto) {
      if (foto.startsWith('http')) {
        return foto
      } else {
        return `http://localhost:3000/upload/foto/${foto}`
      }
    },
    formatDateForInput(dateString) {
      const date = new Date(dateString);
      return date.toISOString().split('T')[0];
    },
    validateForm() {
      this.errors = {};
      if (!this.user.nama) this.errors.nama = 'Nama is required';
      if (!this.user.nomor) this.errors.nomor = 'Nomor is required';
      if (!this.user.jabatan) this.errors.jabatan = 'Jabatan is required';
      if (!this.user.departemen) this.errors.departemen = 'Departemen is required';
      if (!this.user.status) this.errors.status = 'Status is required';
      if (!this.user.tanggal_masuk) this.errors.tanggal_masuk = 'Tanggal Masuk is required';
      return Object.keys(this.errors).length === 0;
    },
    async updateUser() {
      if (!this.validateForm()) return;

      try {
        const formData = new FormData();
        for (const key in this.user) {
          if (key !== 'foto') {
            formData.append(key, this.user[key]);
          }
        }
        if (this.newFoto) {
          formData.append('foto', this.newFoto);
        }

        const response = await axios.put(`http://localhost:3000/karyawan/api/v1/${this.user.id}`, formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        });
        alert('berhasil melakukan update user')
        window.location.href = 'http://localhost:8080/#/admin/table-list';
        console.log('User updated successfully:', response.data);
      } catch (error) {
        console.error('Error updating user:', error);
      }
    }
  },
  mounted() {
    this.fetchUserDetails();
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
