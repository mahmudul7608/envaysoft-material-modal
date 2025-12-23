<script setup>
import { ref, computed, watch } from "vue";
import { materialCategories } from "../data/materials.js";

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(["close", "select"]);

const categories = ["All", ...materialCategories.map(c => c.name)];

const categoryTags = { "All": [] };
const allMaterials = [];

materialCategories.forEach(cat => {
  categoryTags[cat.name] = ["All", ...cat.tags];
  cat.tags.forEach(t => {
    if (!categoryTags["All"].includes(t)) categoryTags["All"].push(t);
  });
  
  cat.materials.forEach(mat => {
    allMaterials.push({
      ...mat,
      category: cat.name,
      image: mat.image || '', 
      code: mat.id 
    });
  });
});

categoryTags["All"].unshift("All");

const currentTags = computed(() => {
    return categoryTags[activeCategory.value] || ["All"];
});


const materials = computed(() => {
  const targetLength = 28;
  const result = [];
  const source = allMaterials;
  
  if (source.length === 0) return [];

  for (let i = 0; i < targetLength; i++) {
    const sourceItem = source[i % source.length];
    result.push({
      ...sourceItem,
      uniqueId: `${sourceItem.id}_${i}`, 
      id: `${sourceItem.id}_${i}` 
    });
  }
  return result;
});

const activeCategory = ref("All");
const activeTag = ref("All"); 

const search = ref("");
const selectedMaterials = ref([]);

const filteredMaterials = computed(() => {
  return materials.value.filter((item) => {
    const matchCategory =
      activeCategory.value === "All" ||
      item.category === activeCategory.value;

    const matchTag =
      activeTag.value === "All" || item.tag === activeTag.value;

    const matchSearch = item.name
      .toLowerCase()
      .includes(search.value.toLowerCase()) || 
      item.code.toLowerCase().includes(search.value.toLowerCase()) ||
      item.category.toLowerCase().includes(search.value.toLowerCase()) ||
      item.tag.toLowerCase().includes(search.value.toLowerCase());

    return matchCategory && matchTag && matchSearch;
  });
});

watch(activeCategory, () => {
  activeTag.value = "All";
});

const selectMaterial = (item) => {
  const index = selectedMaterials.value.findIndex((m) => m.id === item.id);
  if (index === -1) {
    selectedMaterials.value.push(item);
  } else {
    selectedMaterials.value.splice(index, 1);
  }
};

const isSelected = (item) => {
  return selectedMaterials.value.some((m) => m.id === item.id);
};

const insertMaterial = () => {
  if (selectedMaterials.value.length === 0) return;
  emit("select", selectedMaterials.value);
  emit("close");
  selectedMaterials.value = [];
};
</script>

<template>
  <!-- Overlay if open -->
  <div
    v-if="isOpen"
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm p-4 md:p-8"
  >
    <!-- Modal if open -->
    <div
      class="bg-white w-full max-w-7xl rounded-[20px] p-4 md:p-8 shadow-2xl flex flex-col max-h-[90vh]"
    >
      <!-- Header if open -->
      <div class="flex items-center justify-between mb-6 shrink-0">
        <h2 class="text-xl font-semibold text-gray-800">
          Select Your Material
        </h2>
        <button
          @click="$emit('close')"
          class="p-2 rounded-full hover:bg-gray-100 text-gray-500 hover:text-gray-700 transition-colors"
        >
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>

      <!-- Category + Search if open -->
      <div class="flex flex-col gap-4 mb-5">
        <div class="flex items-center justify-between flex-wrap gap-4">
          <!-- Categories -->
          <div class="flex gap-2 flex-wrap">
            <button
              v-for="cat in categories"
              :key="cat"
              @click="activeCategory = cat"
              :class="[
                'px-4 py-2 rounded-lg border text-sm transition font-medium',
                activeCategory === cat
                  ? 'border-blue-500 text-blue-600 bg-white'
                  : 'border-gray-200 text-gray-600 hover:bg-gray-50',
              ]"
            >
              {{ cat }}
            </button>
          </div>

          <div class="relative group">
            <input
              v-model="search"
              type="text"
              placeholder="Search by name or code..."
              class="border border-gray-200 rounded-lg pl-10 pr-4 py-2.5 w-full sm:w-72 focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-400 shadow-sm transition-all"
            />
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="w-5 h-5 absolute left-3 top-1/2 -translate-y-1/2 text-gray-400 group-focus-within:text-blue-500 transition-colors"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z"
              />
            </svg>
          </div>
        </div>

        <!-- Dynamic Tags if open -->
        <div class="flex gap-2 flex-wrap text-sm items-center">
          <button
            v-for="tag in currentTags"
            :key="tag"
            @click="activeTag = tag"
            :class="[
              'px-3 py-1.5 rounded-md transition-colors duration-200',
              activeTag === tag
                ? 'bg-blue-100 text-blue-600 font-medium'
                : 'bg-transparent text-gray-500 hover:text-gray-700',
            ]"
          >
            {{ tag }}
          </button>
        </div>
      </div>

      <div
        class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-7 gap-4 lg:gap-5 max-h-[450px] overflow-y-auto pr-2 pt-2 pb-2 flex-1 scrollbar-thin scrollbar-thumb-gray-200"
      >
        <div
          v-for="item in filteredMaterials"
          :key="item.uniqueId"
          @click="selectMaterial(item)"
          class="cursor-pointer flex justify-center group/card"
        >
          <div
            :class="[
              'relative overflow-hidden border-2 transition-all duration-300 w-full aspect-square rounded-xl shadow-sm',
              isSelected(item)
                ? 'border-blue-500 ring-2 ring-blue-500/10 scale-[0.98]'
                : 'border-transparent hover:border-blue-200 hover:shadow-md hover:-translate-y-0.5',
            ]"
          >
            <img
              :src="item.image"
              alt=""
              class="w-full h-full object-cover transition-transform duration-500 group-hover/card:scale-110"
            />

            <!-- Code Label -->
            <span
              :class="[
                'absolute bottom-2 left-2 text-[10px] px-2 py-1 rounded-md font-medium tracking-wide shadow-md z-10 transition-colors duration-200',
                isSelected(item) ? 'bg-blue-600 text-white' : 'bg-black/80 text-white'
              ]"
            >
              {{ item.code }}
            </span>

            <!-- Check Indicator -->
            <div
              v-if="isSelected(item)"
              class="absolute top-2 right-2 bg-blue-600 text-white rounded-full p-1 shadow-lg ring-2 ring-white z-20"
            >
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="4" stroke="currentColor" class="w-3 h-3">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" />
                </svg>
            </div>
          </div>
        </div>

        <!-- Empty -->
        <div
          v-if="filteredMaterials.length === 0"
          class="col-span-full flex flex-col items-center justify-center py-20 text-gray-400"
        >
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1" stroke="currentColor" class="w-16 h-16 mb-4 opacity-20">
            <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 7.5L12 11.25L3.75 7.5M12 3L3 7.5V16.5L12 21L21 16.5V7.5L12 3Z" />
          </svg>
          <p class="text-lg font-medium">No materials found</p>
          <p class="text-sm">Try adjusting your filters or search terms</p>
        </div>
      </div>

      <div class="flex justify-end mt-4">
        <button
          :disabled="selectedMaterials.length === 0"
          @click="insertMaterial"
          class="px-8 py-3 rounded-[12px] font-semibold bg-[#E8F2FF] text-[#0066FF] hover:bg-[#D9E9FF] transition-all disabled:opacity-50"
        >
          Insert
        </button>
      </div>
    </div>
  </div>
</template>
