<script setup lang>
defineProps({
  books: {
    type: Array,
    required: true
  }
});

defineEmits(['hapus-buku']);
</script>


<template>
  <div class="bg-white p-6 rounded-lg shadow-md">
    <h2 class="text-xl font-semibold mb-4">Daftar Buku ({{ books.length }})</h2>

    <div v-if="books.length > 0" class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <div v-for="(book, index) in books" :key="index" class="border p-4 rounded-lg hover:shadow-md transition-shadow">
        <div class="flex justify-between">
          <div>
            <h3 class="font-bold text-lg">{{ book.judul }}</h3>
            <p class="text-gray-600">{{ book.pengarang }}</p>
            <p class="text-sm text-gray-500">Tahun: {{ book.tahun }}</p>
          </div>
          <button
            @click="$emit('hapus-buku', index)"
            class="text-red-500 hover:text-red-700 h-6"
            title="Hapus buku"
          >
            ✕
          </button>
        </div>
      </div>
    </div>

    <p v-else class="text-gray-500">Tidak ada buku yang ditemukan.</p>
  </div>
</template>
