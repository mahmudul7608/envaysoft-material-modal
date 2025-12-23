<script setup>
import { ref } from "vue";
import AddMaterialBox from "./components/AddMaterialBox.vue";
import MaterialModal from "./components/MaterialModal.vue";

const isModalOpen = ref(false);
const myMaterials = ref([]);
const bgClass = ref("bg-white");

const handleOpenModal = () => {
    isModalOpen.value = true;
    bgClass.value = "bg-[#525659]"; 
};

const handleSelection = (materials) => {
    materials.forEach(newMat => {
      if (!myMaterials.value.some(m => m.id === newMat.id)) {
        myMaterials.value.push(newMat);
      }
    });
    
    isModalOpen.value = false;
    bgClass.value = "bg-white";
};

const removeMaterial = (id) => {
  myMaterials.value = myMaterials.value.filter(m => m.id !== id);
};

const handleClose = () => {
    isModalOpen.value = false;
    bgClass.value = "bg-white";
}
</script>

<template>
  <section :class="['min-h-screen transition-colors duration-500', bgClass]">
    <div class="max-w-[1440px] mx-auto px-4 sm:px-8 md:px-[89px] pt-[72px] pb-12 flex flex-wrap gap-6 justify-center md:justify-start">
      <AddMaterialBox 
        v-for="material in myMaterials" 
        :key="material.uniqueId || material.id"
        :selectedMaterial="material"
        @clear="removeMaterial(material.id)"
      />

      <!-- Add Material Box (Shows ONLY if no materials are selected) -->
      <AddMaterialBox 
        v-if="!isModalOpen && myMaterials.length === 0"
        :selectedMaterial="null"
        @openModal="handleOpenModal" 
      />
    </div>

    <!-- Modal Portal -->
    <MaterialModal 
        :isOpen="isModalOpen" 
        @close="handleClose" 
        @select="handleSelection"
    />
  </section>
</template>
