<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card class="card-plain">
            <template slot="header">
              <h4 class="card-title">Daftar Karyawan</h4>
              <div class="button-group">
                <button @click="goToAddEmployee" class="btn btn-primary">Tambah Karyawan</button>
                <button @click="importCSV" class="btn btn-secondary">Import CSV</button>
                <button @click="exportCSV" class="btn btn-secondary">Export CSV</button>
                <button @click="exportPDF" class="btn btn-secondary">Export PDF</button>
                <input type="file" ref="fileInput" @change="handleFileUpload" style="display: none;">
              </div>
            </template>
            <div class="table-responsive">
              <l-table class="table-hover" :columns="table.columns" :data="table.data">
                <template slot="columns">
                  <th v-for="column in table.columns" :key="column">{{ column }}</th>
                </template>
                <template slot-scope="{row}">
                  <td>{{ row.id }}</td>
                  <td>{{ row.nama }}</td>
                  <td>{{ row.nomor }}</td>
                  <td>{{ row.jabatan }}</td>
                  <td>{{ row.departemen }}</td>
                  <td><img :src="getFotoUrl(row.foto)" alt="Foto Karyawan" width="50" height="50"></td>
                  <td>{{ row.status }}</td>
                  <td>{{ formatDate(row.tanggal_masuk) }}</td>
                  <td>
                    <button @click="editKaryawan(row)" class="btn btn-sm btn-primary mr-2">Edit</button>
                    <button @click="hapusKaryawan(row.id)" class="btn btn-sm btn-danger">Hapus</button>
                  </td>
                </template>
              </l-table>
            </div>
          </card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import LTable from 'src/components/Table.vue'
import Card from 'src/components/Cards/Card.vue'
import axios from 'axios'

export default {
  components: {
    LTable,
    Card
  },
  data() {
    return {
      table: {
        columns: ['ID', 'Nama', 'Nomor', 'Jabatan', 'Departemen', 'Foto', 'Status', 'Tanggal Masuk', 'Aksi'],
        data: []
      }
    }
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get('http://localhost:3000/karyawan/api/v1')
        this.table.data = response.data.data
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    formatDate(dateString) {
      return new Date(dateString).toLocaleDateString('id-ID')
    },
    getFotoUrl(foto) {
      if (foto.startsWith('http')) {
        return foto
      } else {
        return `http://localhost:3000/upload/foto/${foto}`
      }
    },
    editKaryawan(karyawan) {
      this.$router.push({ name: 'User', params: { id: karyawan.id } });
    },
    async hapusKaryawan(id) {
      try {
        const response = await axios.delete(`http://localhost:3000/karyawan/api/v1/${id}`, {
          headers: {
            'Content-Type': 'application/json'
          }
        });
        alert('Berhasil menghapus karyawan');
        this.fetchData();
      } catch (error) {
        console.error('Error deleting employee:', error);
      }
    },
    goToAddEmployee() {
      window.location.href = 'http://localhost:8080/#/admin/add-user';
    },
    importCSV() {
      this.$refs.fileInput.click();
    },
    async handleFileUpload(event) {
      const file = event.target.files[0];
      if (!file) return;

      const formData = new FormData();
      formData.append('file', file);

      try {
        const response = await axios.post('http://localhost:3000/karyawan/import/api/v1', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        });
        console.log('CSV import successful:', response.data);
        alert('berhasil melakukan import file')
        this.fetchData();
      } catch (error) {
        console.error('Error importing CSV:', error);
      }
      event.target.value = '';
    },
    async exportCSV() {
      try {
        const response = await axios.get('http://localhost:3000/karyawan/export/api/v1', {
          responseType: 'blob'
        });

        const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'karyawan.csv');
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
      } catch (error) {
        console.error('Error exporting CSV:', error);
      }
    },
    async exportPDF() {
      try {
        const response = await axios.get('http://localhost:3000/karyawan/generate-pdf/api/v1', {
          responseType: 'blob'
        });

        const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'karyawan.pdf');
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
      } catch (error) {
        console.error('Error exporting PDF:', error);
      }
    }
  },
  mounted() {
    this.fetchData()
  }
}
</script>

<style>
.btn-sm {
  padding: 0.25rem 0.5rem;
  font-size: 0.875rem;
}

.mr-2 {
  margin-right: 0.5rem;
}

.button-group {
  margin-bottom: 15px;
}

.button-group button {
  margin-right: 10px;
}

.search-bar {
  margin-bottom: 15px;
}
</style>
