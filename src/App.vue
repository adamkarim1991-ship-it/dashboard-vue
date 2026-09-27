<script setup>
import { ref, computed, onMounted } from 'vue'
import UserCard from './components/UserCard.vue'

/* ============================================================
   STATE DASAR
   ============================================================ */
const users = ref([])
const sedangMemuat = ref(false)
const pesanError = ref('')

/* ============================================================
   STEP 8 — FITUR PENCARIAN
   ============================================================ */
const queryPencarian = ref('')

// Computed hasil pencarian — menjadi sumber untuk Soal 2 (chained computed)
const penggunaTersaring = computed(() => {
  const q = queryPencarian.value.toLowerCase().trim()
  if (!q) return users.value
  return users.value.filter((user) =>
    user.name.toLowerCase().includes(q)
  )
})

/* ============================================================
   SOAL 2 — SORT CONTROL (CHAINED COMPUTED)
   ============================================================ */
// ref untuk menampung arah urutan aktif: 'asc' atau 'desc'
const arahuRutan = ref('asc')

// Computed BARU yang sumbernya adalah penggunaTersaring (bukan users)
// Inilah yang disebut chained computed: filter dulu, baru diurutkan
const penggunaTerurut = computed(() => {
  // PENTING: sort SALINAN array ([...spread]), jangan mutate array asli
  const salinan = [...penggunaTersaring.value]
  salinan.sort((a, b) => {
    if (arahuRutan.value === 'asc') {
      return a.name.localeCompare(b.name)
    } else {
      return b.name.localeCompare(a.name)
    }
  })
  return salinan
})

/* ============================================================
   STEP 3 — AMBIL DATA DARI JSONPLACEHOLDER
   ============================================================ */
async function muatPengguna() {
  sedangMemuat.value = true
  pesanError.value = ''
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/users')
    if (!res.ok) throw new Error('Gagal mengambil data')
    users.value = await res.json()
  } catch (err) {
    pesanError.value = err.message
  } finally {
    sedangMemuat.value = false
  }
}

onMounted(muatPengguna)
</script>

<template>
  <div class="app">
    <h1>Dashboard Vue — Pertemuan 5</h1>

    <!-- Toolbar: tombol muat + kolom pencarian -->
    <div class="toolbar">
      <button @click="muatPengguna">Muat Pengguna</button>
      <input
        v-model="queryPencarian"
        type="text"
        placeholder="Cari username..."
      />
    </div>

    <!-- SOAL 2: Kontrol urutan -->
    <div class="kontrol-urutan">
      <button
        :class="{ aktif: arahuRutan === 'asc' }"
        @click="arahuRutan = 'asc'"
      >
        Urtukan A–Z
      </button>
      <button
        :class="{ aktif: arahuRutan === 'desc' }"
        @click="arahuRutan = 'desc'"
      >
        Urtukan Z–A
      </button>
    </div>

    <!-- State: loading / error / empty -->
    <p v-if="sedangMemuat">Sedang memuat data...</p>
    <p v-else-if="pesanError" class="error">{{ pesanError }}</p>
    <p v-else-if="penggunaTerurut.length === 0">
      Tidak ada pengguna yang cocok.
    </p>

    <!-- Daftar user — v-for baca dari penggunaTerurut (chained computed) -->
    <div v-else class="daftar-user">
      <UserCard
        v-for="user in penggunaTerurut"
        :key="user.id"
        :nama="user.name"
        :email="user.email"
        :telepon="user.phone"
        :perusahaan="user.company.name"
        :kota="user.address.city"
      />
    </div>
  </div>
</template>

<style scoped>
.app {
  max-width: 720px;
  margin: 0 auto;
  padding: 1rem;
  font-family: Arial, sans-serif;
}

h1 {
  color: #1f4e79;
}

.toolbar {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.toolbar input {
  flex: 1;
  padding: 0.4rem 0.6rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.toolbar button {
  padding: 0.4rem 0.8rem;
  background: #1f4e79;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.kontrol-urutan {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.kontrol-urutan button {
  padding: 0.4rem 0.8rem;
  border: 1px solid #ccc;
  background: #fff;
  border-radius: 4px;
  cursor: pointer;
}

/* Tombol aktif di-highlight */
.kontrol-urutan button.aktif {
  background: #1f4e79;
  color: #fff;
  border-color: #1f4e79;
}

.daftar-user {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.error {
  color: red;
}
</style>