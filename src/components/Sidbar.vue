<script setup>
import { ref } from "vue";
import { Home, Zap, BarChart2, Clock, BarChart, Clock1 } from "lucide-vue-next";

const currentView = ref("dashboard");

const emit = defineEmits(['update-view']);

const changeView = (view)=>{
    currentView.value = view;
    emit("update-view", view)
}

</script>

<template>
  <div class="sidebar h-screen bg-gray-800 text-white p-4">
    <div class="flex items-center my-4">
      <img
        src="data:image/png;base64,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"
        alt=""
        class="w-[60px] rounded-lg mr-2"
      />
      <h3 class="sidebar-title font-bold text-3xl">Potato</h3>
    </div>

    <ul class="sidebar-menu mt-6 flex flex-col items-start space-y-4 mt-[50px]">
      <li class="w-full">
        <button
          class="flex items-center py-2 px-4 w-full border rounded-lg hover:bg-blue-500 transition-all space-x-2"
            @click="changeView('dashboard')"
        >
          <Home class="w-5 h-5" /> <span>Dashboard</span>
        </button>
      </li>
      <li class="w-full">
        <button
          class="flex items-center py-2 px-4 w-full border rounded-lg hover:bg-blue-500 transition-all space-x-2"
            @click="changeView('predictions')"
        >
          <Zap class="w-5 h-5" /> <span>Make Predictions</span>
        </button>
      </li>
      <li class="w-full">
        <button
          class="flex items-center py-2 px-4 w-full border rounded-lg hover:bg-blue-500 transition-all space-x-2"
            @click="changeView('analysis')"
        >
          <BarChart2 class="w-5 h-5" /> <span>Analysis</span>
        </button>
      </li>
      <li class="w-full">
        <button
          class="flex items-center py-2 px-4 w-full border rounded-lg hover:bg-blue-500 transition-all space-x-2"
            @click="changeView('history')"
        >
          <Clock class="w-5 h-5" /> <span>History</span>
        </button>
      </li>
    </ul>
  </div>
</template>
