<script setup>
defineProps({
  book: {
    type: Object,
    required: true
  }
});

defineEmits(['show-details']);
</script>


<template>
  <div class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-xl transition-all duration-300 border border-gray-100">
    <div class="h-2 bg-gradient-to-r from-purple-400 to-blue-500"></div>
    <div class="p-5">
      <div class="flex justify-between items-start">
        <div>
          <h3 class="text-xl font-bold text-gray-800 mb-1">{{ book.judul }}</h3>
          <p class="text-indigo-600 font-medium">{{ book.pengarang }}</p>
          <div class="flex items-center mt-3">
            <span class="bg-blue-100 text-blue-800 text-xs font-semibold px-2.5 py-0.5 rounded">
              {{ book.tahun }}
            </span>
          </div>
        </div>
        <button @click="$emit('show-details')" class="text-gray-400 hover:text-indigo-600 transition-colors">
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
          </svg>
        </button>
      </div>
    </div>
  </div>
</template>
