<template>
  <div>
    <div class="card">
      <h2>Buku Tamu</h2>
      <p class="subtitle">Teaching Factory PPLG - SMKN 4 Tasikmalaya</p>

      <div class="toolbar">
        <button class="btn btn-dark btn-lg" data-bs-toggle="modal" data-bs-target="#form-modal">+ Tambah</button>
        <input type="text" id="search" placeholder="Cari data..." onkeyup="searchTable()">
      </div>
      <table id="table">
        <thead>
          <tr>
            <th>No</th>
            <th>Tanggal</th>
            <th>Nama Lengkap</th>
            <th>Nomor Telepon</th>
            <th>Alamat / Lisensi</th>
            <th>Tujuan Kunjungan</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(record, i) in records" :key="record.id">
            <td>{{ i+1 }}.</td>
            <td>{{ record.tanggal }}</td>
            <td>{{ record.nama }}</td>
            <td>{{ record.nomor }}</td>
            <td>{{ record.alamat }}</td>
            <td>{{ record.tujuan_kunjungan }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- MODAL FORM -->
     <div class="modal fade" id="form-modal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Modal title</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="submitForm">
              <div class="mb-4">
                <label for="nama">Nama Lengkap</label>
                <input v-model="form.nama" type="text" class="form-control" placeholder="Nama Lengkap">
              </div>
              <div class="mb-4">
                <label for="nomor">Nomor Telepon</label>
                <input v-model="form.nomor" type="text" class="form-control" placeholder="Nomor Telepon">
              </div>
              <div class="mb-4">
                <label for="alamat">Alamat / Instansi</label>
                <input v-model="form.alamat" type="text" class="form-control" placeholder="Alamat / Lisensi">
              </div>
              <div class="mb-4">
                <label for="tujuan_kunjungan">Tujuan Kunjungan</label>
                <input v-model="form.tujuan_kunjungan" type="text" class="form-control" placeholder="Tujuan Kunjungan">
              </div>
              <button type="submit" class="btn btn-primary">Kirim</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const client = useSupabaseClient()
console.log(client)

const form = ref({
  nama: '',
  nomor: '',
  alamat: '',
  tujuan_kunjungan: ''
})

const records = ref([])

async function submitForm() {
  console.log('Submitting form:', form.value)
  const { data, error } = await client
    .from('bukutamu')
    .insert([form.value])
  
  if (error) {
    console.error('Error inserting data:', error)
  } else {
    console.log('Data inserted successfully:', data)
    // Reset form
    form.value.nama = ''
    form.value.nomor = ''
    form.value.alamat = ''
    form.value.tujuan_kunjungan = ''
    fetchData()
  }
}

async function fetchData() {
  const { data, error } = await client
    .from('bukutamu')
    .select('*')
  
  if (error) {
    console.error('Error fetching data:', error)
  } else {
    records.value = data
  }
}

onMounted(() => {
  fetchData()
})
</script>
