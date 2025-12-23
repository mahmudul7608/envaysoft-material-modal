<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  selectedMaterial: Object
});

const emit = defineEmits(["openModal", "clear"]);

const openModal = () => {
  emit("openModal", true);
};
</script>

<template>
  <div class="relative group">
    <!-- Box Container -->
    <button 
      @click="openModal" 
      :class="[
        'flex justify-center items-center flex-col w-[280px] h-[280px] sm:w-[300px] sm:h-[300px] border rounded-[20px] gap-[12px] overflow-hidden relative transition-all duration-300 bg-white shadow-sm cursor-pointer',
        selectedMaterial 
          ? 'border-gray-200 hover:border-blue-300' 
          : 'border-[#C7C7C7] hover:border-blue-400 hover:shadow-md hover:bg-blue-50/10 active:scale-95'
      ]"
    >
      <!-- Selected State -->
      <div v-if="selectedMaterial" class="w-full h-full relative group/img">
          <img :src="selectedMaterial.image" class="w-full h-full object-cover transition-transform duration-500 group-hover/img:scale-110" />
          
          <!-- Code Badge -->
          <div class="absolute bottom-4 left-4 bg-black/80 backdrop-blur-sm text-white text-xs px-3 py-1.5 rounded-lg font-medium tracking-wide shadow-lg z-10 transition-transform group-hover/img:scale-105">
            {{ selectedMaterial.code || selectedMaterial.id }}
          </div>

          <div class="absolute inset-0 bg-black/5 opacity-0 group-hover/img:opacity-100 transition-opacity flex items-center justify-center">
             <span class="bg-white/90 backdrop-blur-sm text-gray-800 text-xs font-semibold px-3 py-1.5 rounded-full shadow-lg opacity-0 group-hover/img:opacity-100 transition-all translate-y-2 group-hover/img:translate-y-0">
               Change Material
             </span>
          </div>
      </div>

      <!-- Empty State -->
      <template v-else>
          <div class="flex flex-col items-center gap-3 group-hover:scale-110 transition-transform duration-300">
            <div class="p-5 bg-gray-50 rounded-full group-hover:bg-blue-50 transition-colors duration-300">
              <svg width="40" height="40" viewBox="0 0 50 50" fill="none" xmlns="http://www.w3.org/2000/svg" class="text-gray-400 group-hover:text-blue-500 transition-colors">
                <path d="M23.9771 6.22919C14.6375 6.22919 9.96663 6.22919 7.06663 9.12919C4.16663 12.0292 4.16455 16.6979 4.16455 26.0334C4.16455 35.3688 4.16455 40.0375 7.06663 42.9375C9.96663 45.8375 14.6375 45.8375 23.975 45.8375C33.3146 45.8375 37.9854 45.8375 40.8854 42.9375C43.7854 40.0375 43.7875 35.3688 43.7875 26.0334V24.9917" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
                <path d="M10.4146 43.7292C19.1854 33.8313 29.0416 20.7021 43.7479 30.5479" stroke="currentColor" stroke-width="2"/>
                <path d="M37.4916 4.16251V20.8458M45.8333 12.4521L29.1458 12.4833" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
              </svg>
            </div>

            <p class="font-poppins text-sm font-medium text-gray-500 group-hover:text-blue-600 transition-colors">
              Add Material
            </p>
          </div>
      </template>
    </button>
    
    <!-- Remove Button -->
    <button
        v-if="selectedMaterial"
        @click.stop="$emit('clear')"
        class="absolute -top-2 -right-2 bg-white w-8 h-8 rounded-full flex items-center justify-center shadow-lg border border-gray-100 text-gray-400 hover:text-red-500 hover:border-red-100 hover:scale-110 transition-all z-30 cursor-pointer group/remove"
        title="Remove material"
    >
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor" class="w-4 h-4 transition-transform group-hover/remove:rotate-90">
            <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
        </svg>
    </button>
  </div>
</template>


