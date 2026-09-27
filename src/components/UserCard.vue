<script setup>
import { ref } from 'vue'

/* ============================================================
   PROPS — data user dari App.vue
   ============================================================ */
const props = defineProps({
  nama: String,
  email: String,
  telepon: String,
  perusahaan: String,
  kota: String,
})

/* ============================================================
   SOAL 1 — LOCAL STATE
   Status buka/tutup detail ini murni milik komponen ini sendiri.
   Bukan prop dari App.vue, bukan state App.vue.
   ============================================================ */
const detailTerbuka = ref(false)
</script>

<template>
  <div class="card">
    <div class="header">
      <div>
        <h3>{{ nama }}</h3>
        <p class="email">{{ email }}</p>
      </div>

      <!-- Tombol toggle: label berubah sesuai state lokal -->
      <button
        :class="{ aktif: detailTerbuka }"
        @click="detailTerbuka = !detailTerbuka"
      >
        {{ detailTerbuka ? 'Sembunyikan' : 'Lihat Detail' }}
      </button>
    </div>

    <!-- Detail — hanya tampil kalau detailTerbuka === true -->
    <div v-if="detailTerbuka" class="detail">
      <p>Telepon: {{ telepon }}</p>
      <p>Perusahaan: {{ perusahaan }}</p>
      <p>Kota: {{ kota }}</p>
    </div>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 0.75rem 1rem;
  background: #fff;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 1rem;
}

h3 {
  margin: 0 0 0.25rem 0;
}

.email {
  margin: 0;
  color: #888;
  font-size: 0.9rem;
}

button {
  padding: 0.35rem 0.7rem;
  border: 1px solid #ccc;
  background: #fff;
  border-radius: 4px;
  cursor: pointer;
  white-space: nowrap;
}

button.aktif {
  background: #1f4e79;
  color: #fff;
  border-color: #1f4e79;
}

.detail {
  margin-top: 0.75rem;
  padding: 0.6rem 0.8rem;
  background: #f4f6f8;
  border-radius: 6px;
  font-size: 0.9rem;
}

.detail p {
  margin: 0.2rem 0;
}
</style>