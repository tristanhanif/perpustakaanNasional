<template>
  <div class="container mx-auto px-4 py-10 min-h-screen w-screen ">
    <!-- Header -->
    <header class="mb-10 text-center">
      <h1 class="text-4xl font-bold text-indigo-700 mb-2">Perpustakaan Mini</h1>
      <p class="text-gray-600">Sistem Manajemen Buku Sederhana</p>
    </header>

    <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
      <!-- Form Tambah Buku -->
      <div class="lg:col-span-1 bg-white rounded-xl shadow-md p-6 h-fit">
        <h2 class="text-xl font-semibold mb-4 text-indigo-600 border-b pb-2">Tambah Buku Baru</h2>
        <form @submit.prevent="addBook" class="space-y-4">
          <div>
            <label for="title" class="block text-sm font-medium text-gray-700 mb-1">Judul Buku*</label>
            <input v-model="newBook.title" type="text" id="title" placeholder="Masukkan judul buku"
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500">
            <p v-if="errors.title" class="mt-1 text-sm text-red-600">{{ errors.title }}</p>
          </div>

          <div>
            <label for="author" class="block text-sm font-medium text-gray-700 mb-1">Pengarang*</label>
            <input v-model="newBook.author" type="text" id="author" placeholder="Nama pengarang"
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500">
            <p v-if="errors.author" class="mt-1 text-sm text-red-600">{{ errors.author }}</p>
          </div>

          <div>
            <label for="year" class="block text-sm font-medium text-gray-700 mb-1">Tahun Terbit</label>
            <input v-model="newBook.year" type="number" id="year" placeholder="Tahun terbit"
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500">
          </div>

          <button type="submit"
            class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-medium py-2 px-4 rounded-md transition duration-200 flex items-center justify-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd"
                d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z"
                clip-rule="evenodd" />
            </svg>
            Tambah Buku
          </button>
        </form>
      </div>

      <!-- Konten Utama -->
      <div class="lg:col-span-2 space-y-6">
        <!-- Pencarian -->
        <div class="bg-white rounded-xl shadow-md p-6">
          <h2 class="text-xl font-semibold mb-4 text-indigo-600 border-b pb-2">Cari Buku</h2>
          <div class="flex flex-col sm:flex-row gap-4">
            <div class="flex-1">
              <label class="block text-sm font-medium text-gray-700 mb-1">Cari berdasarkan:</label>
              <div class="flex rounded-md shadow-sm">
                <select v-model="searchType"
                  class="px-3 py-2 border border-r-0 border-gray-300 rounded-l-md bg-gray-50 text-gray-500 text-sm focus:outline-none focus:ring-1 focus:ring-indigo-500">
                  <option value="title">Judul</option>
                  <option value="author">Pengarang</option>
                </select>
                <input v-model="searchQuery" type="text" placeholder="Masukkan pencarian"
                  class="flex-1 min-w-0 block w-full px-3 py-2 border border-gray-300 focus:outline-none focus:ring-1 focus:ring-indigo-500">
                <button @click="searchBooks"
                  class="inline-flex items-center px-4 py-2 border border-l-0 border-gray-300 bg-indigo-600 text-white font-medium rounded-r-md hover:bg-indigo-700 focus:outline-none focus:ring-1 focus:ring-indigo-500">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd"
                      d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
                      clip-rule="evenodd" />
                  </svg>
                </button>
              </div>
            </div>
            <div class="sm:w-auto">
              <button @click="resetSearch"
                class="h-full px-4 py-2 border border-gray-300 bg-white text-gray-700 font-medium rounded-md hover:bg-gray-50 focus:outline-none focus:ring-1 focus:ring-indigo-500">
                Reset
              </button>
            </div>
          </div>
        </div>

        <!-- Daftar Buku -->
        <div class="bg-white rounded-xl shadow-md overflow-hidden">
          <div class="px-6 py-4 border-b border-gray-200 flex justify-between items-center">
            <h2 class="text-xl font-semibold text-indigo-600">Daftar Buku</h2>
            <span class="text-sm text-gray-500">{{ filteredBooks.length }} buku ditemukan</span>
          </div>

          <!-- Empty state -->
          <div v-if="filteredBooks.length === 0" class="p-8 text-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mx-auto text-gray-400" fill="none"
              viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1"
                d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253" />
            </svg>
            <h3 class="mt-2 text-lg font-medium text-gray-900">
              {{ books.length === 0 ? 'Belum ada buku yang terdaftar' : 'Tidak ada buku yang sesuai' }}
            </h3>
            <p class="mt-1 text-sm text-gray-500">
              {{ books.length === 0 ? 'Tambahkan buku pertama Anda' : 'Coba ubah kriteria pencarian' }}
            </p>
          </div>

          <!-- Modal Rating -->
          <div v-if="showRatingModal"
            class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center p-4 z-50">
            <div class="bg-white rounded-lg shadow-xl max-w-md w-full p-6">
              <h3 class="text-lg font-medium text-gray-900 mb-4">Beri Rating untuk "{{ currentBook.title }}"</h3>
              <div class="flex items-center mb-4">
                <span class="mr-2">Rating:</span>
                <div class="flex">
                  <svg v-for="i in 5" :key="i" @click="setRating(i)"
                    :class="['h-6 w-6 cursor-pointer', i <= newRating.rating ? 'text-yellow-400' : 'text-gray-300']"
                    fill="currentColor" viewBox="0 0 20 20">
                    <path
                      d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
                  </svg>
                </div>
              </div>
              <textarea v-model="newRating.review" placeholder="Tulis ulasan Anda..."
                class="w-full px-3 py-2 border border-gray-300 rounded-md mb-4"></textarea>
              <div class="flex justify-end space-x-3">
                <button @click="showRatingModal = false"
                  class="px-4 py-2 border border-gray-300 rounded-md">Batal</button>
                <button @click="submitRating" class="px-4 py-2 bg-indigo-600 text-white rounded-md">Simpan</button>
              </div>
            </div>
          </div>

          <!-- Di tabel buku, tambahkan kolom Rating -->
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  No</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Judul</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Pengarang</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Tahun</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Rating</th>
                <th scope="col" class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Aksi</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="(book, index) in filteredBooks" :key="book.id" class="hover:bg-gray-50">
                <!-- Kolom-kolom sebelumnya -->
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ index + 1 }}</td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm font-medium text-gray-900">{{ book.title }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-500">{{ book.author }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-500">{{ book.year || '-' }}</div>
                </td>

                <!-- Kolom Rating yang diperbaiki -->
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center">
                    <svg v-for="i in 5" :key="i"
                      :class="['h-4 w-4', i <= (book.rating || 0) ? 'text-yellow-400' : 'text-gray-300']"
                      fill="currentColor" viewBox="0 0 20 20">
                      <path
                        d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
                    </svg>
                    <span class="ml-1 text-xs text-gray-500">({{ book.reviews ? book.reviews.length : 0 }})</span>
                  </div>
                </td>

                <!-- Kolom Aksi -->
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-2">
                  <button @click="openRatingModal(book)"
                    class="text-indigo-600 hover:text-indigo-900 flex items-center space-x-1">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24"
                      stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M11.049 2.927c.3-.921 1.603-.921 1.902 0l1.519 4.674a1 1 0 00.95.69h4.915c.969 0 1.371 1.24.588 1.81l-3.976 2.888a1 1 0 00-.363 1.118l1.518 4.674c.3.922-.755 1.688-1.538 1.118l-3.976-2.888a1 1 0 00-1.176 0l-3.976 2.888c-.783.57-1.838-.197-1.538-1.118l1.518-4.674a1 1 0 00-.363-1.118l-3.976-2.888c-.784-.57-.38-1.81.588-1.81h4.914a1 1 0 00.951-.69l1.519-4.674z" />
                    </svg>
                    <span>Rate</span>
                  </button>
                  <button @click="confirmDelete(book.id)"
                    class="text-red-600 hover:text-red-900 flex items-center space-x-1">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24"
                      stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                    </svg>
                    <span>Hapus</span>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Modal Konfirmasi Hapus -->
    <div v-if="showDeleteModal"
      class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center p-4 z-50">
      <div class="bg-white rounded-lg shadow-xl max-w-md w-full p-6">
        <div class="flex items-start">
          <div class="flex-shrink-0 flex items-center justify-center h-12 w-12 rounded-full bg-red-100 mx-auto">
            <svg class="h-6 w-6 text-red-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
            </svg>
          </div>
          <div class="ml-4">
            <h3 class="text-lg font-medium text-gray-900">Konfirmasi Hapus</h3>
            <div class="mt-2">
              <p class="text-sm text-gray-500">Apakah Anda yakin ingin menghapus buku ini? Tindakan ini tidak dapat
                dibatalkan.</p>
            </div>
          </div>
        </div>
        <div class="mt-5 sm:mt-6 flex justify-end space-x-3">
          <button @click="showDeleteModal = false" type="button"
            class="px-4 py-2 border border-gray-300 bg-white text-gray-700 font-medium rounded-md shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
            Batal
          </button>
          <button @click="deleteBook" type="button"
            class="px-4 py-2 border border-transparent bg-red-600 text-white font-medium rounded-md shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500">
            Hapus
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// Data buku
const books = ref([])
const nextId = ref(1)

// Form buku baru
const newBook = ref({
  title: '',
  author: '',
  year: '',
  rating: 0,
  reviews: []
})

// Validasi
const errors = ref({
  title: '',
  author: ''
})

// Pencarian
const searchQuery = ref('')
const searchType = ref('title')
const isSearching = ref(false)

// Modal
const showDeleteModal = ref(false)
const bookToDelete = ref(null)

// Buku yang difilter
const filteredBooks = computed(() => {
  if (!isSearching.value) return books.value

  const query = searchQuery.value.toLowerCase()
  return books.value.filter(book => {
    if (searchType.value === 'author') {
      return book.author.toLowerCase().includes(query)
    } else {
      return book.title.toLowerCase().includes(query)
    }
  })
})

// Validasi form
const validate = () => {
  let isValid = true
  errors.value = { title: '', author: '' }

  if (!newBook.value.title.trim()) {
    errors.value.title = 'Judul buku harus diisi'
    isValid = false
  }

  if (!newBook.value.author.trim()) {
    errors.value.author = 'Pengarang harus diisi'
    isValid = false
  }

  return isValid
}

// Cek duplikat
const isDuplicate = (title, author) => {
  return books.value.some(
    book => book.title.toLowerCase() === title.toLowerCase() &&
      book.author.toLowerCase() === author.toLowerCase()
  )
}

// Tambah buku
const addBook = () => {
  if (!validate()) return

  const title = newBook.value.title.trim()
  const author = newBook.value.author.trim()

  if (isDuplicate(title, author)) {
    alert('Buku dengan judul dan pengarang yang sama sudah ada!')
    return
  }

  books.value.unshift({
    id: nextId.value++,
    title: title,
    author: author,
    year: newBook.value.year || undefined
  })

  // Reset form
  newBook.value = { title: '', author: '', year: '' }

  // Reset pencarian jika sedang aktif
  if (isSearching.value) {
    resetSearch()
  }
}

// Konfirmasi hapus
const confirmDelete = (id) => {
  bookToDelete.value = id
  showDeleteModal.value = true
}

// Hapus buku
const deleteBook = () => {
  books.value = books.value.filter(book => book.id !== bookToDelete.value)
  showDeleteModal.value = false
  bookToDelete.value = null
}

// Cari buku
const searchBooks = () => {
  isSearching.value = searchQuery.value.trim() !== ''
}

// Reset pencarian
const resetSearch = () => {
  searchQuery.value = ''
  isSearching.value = false
}

// Load data contoh saat pertama kali
onMounted(() => {
  const sampleBooks = [
    { id: nextId.value++, title: 'Pemrograman JavaScript Modern', author: 'John Doe', year: 2022, rating: 4, reviews: [4] },
    { id: nextId.value++, title: 'Belajar React.js', author: 'Jane Smith', year: 2021, rating: 5, reviews: [5] },
    { id: nextId.value++, title: 'Desain Grafis untuk Pemula', author: 'Michael Johnson', year: 2020, rating: 3, reviews: [3] },
    { id: nextId.value++, title: 'Pengembangan Aplikasi Mobile', author: 'Sarah Williams', year: 2019, rating: 4, reviews: [4] },
  ]
  books.value = sampleBooks
})

// Fitur Rating
const showRatingModal = ref(false)
const currentBook = ref(null)
const newRating = ref({
  rating: 0,
  review: ''
})

const openRatingModal = (book) => {
  currentBook.value = book
  newRating.value = { rating: 0, review: '' }
  showRatingModal.value = true
}

const setRating = (stars) => {
  newRating.value.rating = stars
}

const submitRating = () => {
  if (!currentBook.value.ratings) {
    currentBook.value.ratings = []
  }
  currentBook.value.ratings.push({ ...newRating.value })
  showRatingModal.value = false
}
</script>
