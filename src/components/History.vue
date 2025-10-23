<template>
  <div class="history-container p-8">
    <h1 class="text-3xl font-bold mb-6">Prediction History</h1>

    <div
      class="bg-white rounded-2xl shadow-xl p-8 border-l-8 border-purple-500"
    >
      <!-- Header -->
      <div class="flex items-center gap-3 mb-6">
        <div
          class="w-12 h-12 bg-gradient-to-br from-purple-500 to-indigo-600 rounded-xl flex items-center justify-center"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-6 w-6 text-white"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z"
            />
          </svg>
        </div>
        <div>
          <h2 class="text-2xl font-bold text-gray-800">Filter Options</h2>
          <p class="text-sm text-gray-500">Refine your search results</p>
        </div>
      </div>

      <!-- Filters Grid -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <!-- Class Filter -->
        <div class="space-y-2">
          <label
            class="block text-sm font-semibold text-gray-700 uppercase tracking-wide flex items-center gap-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-4 w-4 text-purple-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M7 7h.01M7 3h5c.512 0 1.024.195 1.414.586l7 7a2 2 0 010 2.828l-7 7a2 2 0 01-2.828 0l-7-7A1.994 1.994 0 013 12V7a4 4 0 014-4z"
              />
            </svg>
            Class
          </label>
          <select
            v-model="class_name"
            class="w-full px-4 py-3 bg-gradient-to-r from-purple-50 to-indigo-50 border-2 border-purple-200 rounded-xl text-gray-700 font-medium focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 cursor-pointer hover:border-purple-400"
          >
            <option>All Classes</option>
            <option value="Healthy">🌱 Healthy</option>
            <option value="Late blight">⏰ Late Blight</option>
            <option value="Early blight">🍂 Early Blight</option>
          </select>
        </div>

        <!-- Date Filter -->
        <div class="space-y-2">
          <label
            class="block text-sm font-semibold text-gray-700 uppercase tracking-wide flex items-center gap-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-4 w-4 text-purple-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"
              />
            </svg>
            Date
          </label>
          <select
            class="w-full px-4 py-3 bg-gradient-to-r from-purple-50 to-indigo-50 border-2 border-purple-200 rounded-xl text-gray-700 font-medium focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 cursor-pointer hover:border-purple-400"
          >
            <option value="">All Dates</option>
            <option value="today">📅 Today</option>
            <option value="week">📆 This Week</option>
            <option value="month">🗓️ This Month</option>
            <option value="custom">📋 Custom Date</option>
          </select>
        </div>

        <!-- Confidence Filter -->
        <div class="space-y-2">
          <label
            class="block text-sm font-semibold text-gray-700 uppercase tracking-wide flex items-center gap-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-4 w-4 text-purple-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"
              />
            </svg>
            Confidence
          </label>
          <select
            class="w-full px-4 py-3 bg-gradient-to-r from-purple-50 to-indigo-50 border-2 border-purple-200 rounded-xl text-gray-700 font-medium focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 cursor-pointer hover:border-purple-400"
          >
            <option value="">All Levels</option>
            <option value="high">🟢 High (≥80%)</option>
            <option value="medium">🟡 Medium (50-79%)</option>
            <option value="low">🔴 Low (under 50%)</option>
          </select>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="flex gap-4 mt-8 pt-6 border-t-2 border-gray-100">
        <button
          @click="filtred_by_class(class_name)"
          class="flex-1 bg-gradient-to-r from-purple-600 to-indigo-600 text-white px-6 py-3 rounded-xl font-semibold hover:from-purple-700 hover:to-indigo-700 transform hover:scale-105 transition-all duration-300 shadow-lg flex items-center justify-center gap-2"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z"
            />
          </svg>
          Apply Filters
        </button>
        <button
          class="px-6 py-3 bg-gray-100 text-gray-700 rounded-xl font-semibold hover:bg-gray-200 transition-all duration-300 flex items-center justify-center gap-2"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
            />
          </svg>
          Reset
        </button>
      </div>
    </div>

    <div v-if="clicked">
      <div
        v-for="prediction in filtred_data"
        :key="prediction.id"
        class="flex items-center gap-4 w-full bg-white border-l-4 border-blue-500 rounded-xl shadow hover:shadow-md transition-shadow mt-4"
      >
        <div class="flex flex-col gap-4 w-full p-4 my-2">
          <div class="flex items-center w-full">
            <img
              :src="prediction.image_url"
              alt="Prediction Image"
              class="w-28 h-28 md:w-40 md:h-44 object-cover rounded-2xl border-4 border-white shadow-lg group-hover:scale-105 transition-transform duration-300"
            />

            <div class="flex-1 grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Filename -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-gray-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  File
                </p>
                <p class="font-bold text-gray-800 truncate text-sm">
                  {{ prediction.filename }}
                </p>
              </div>

              <!-- Predicted Class -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-green-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Class
                </p>
                <p class="font-bold text-green-600 text-lg">
                  {{ prediction.predicted_class }}
                </p>
              </div>

              <!-- Confidence -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-blue-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Confidence
                </p>
                <div class="flex items-center gap-2">
                  <p class="font-bold text-blue-600 text-2xl">
                    {{ prediction.confidence }}%
                  </p>
                  <div
                    class="w-3 h-3 rounded-full animate-pulse"
                    :class="{
                      'bg-green-500': prediction.confidence >= 80,
                      'bg-yellow-500':
                        prediction.confidence >= 50 &&
                        prediction.confidence < 80,
                      'bg-red-500': prediction.confidence < 50,
                    }"
                  ></div>
                </div>
              </div>

              <!-- Date -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-gray-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Date
                </p>
                <p class="font-semibold text-gray-700 text-sm">
                  {{
                    new Date(prediction.createdAt).toLocaleDateString("en-US", {
                      year: "numeric",
                      month: "short",
                      day: "numeric",
                    })
                  }}
                </p>
              </div>
            </div>
          </div>
          <div
            @click="delete prediction.id"
            class="w-full py-4 rounded-xl bg-red-500 text-white text-2xl font-bold cursor-pointer hover:bg-red-400 transition-all text-center"
          >
            DELETE
          </div>
        </div>

        <!-- <div class="mr-4">
          <progress class="progress progress-accent w-60 rounded-full" value="85" max="100"></progress>
        </div> -->
      </div>
    </div>

    <div v-else>
      <div
        v-for="prediction in historyData"
        :key="prediction.id"
        class="flex items-center gap-4 w-full bg-white border-l-4 border-blue-500 rounded-xl shadow hover:shadow-md transition-shadow mt-4"
      >
        <div class="flex flex-col gap-4 w-full p-4 my-2">
          <div class="flex items-center w-full">
            <img
              :src="prediction.image_url"
              alt="Prediction Image"
              class="w-28 h-28 md:w-40 md:h-44 object-cover rounded-2xl border-4 border-white shadow-lg group-hover:scale-105 transition-transform duration-300"
            />

            <div class="flex-1 grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Filename -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-gray-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  File
                </p>
                <p class="font-bold text-gray-800 truncate text-sm">
                  {{ prediction.filename }}
                </p>
              </div>

              <!-- Predicted Class -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-green-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Class
                </p>
                <p class="font-bold text-green-600 text-lg">
                  {{ prediction.predicted_class }}
                </p>
              </div>

              <!-- Confidence -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-blue-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Confidence
                </p>
                <div class="flex items-center gap-2">
                  <p class="font-bold text-blue-600 text-2xl">
                    {{ prediction.confidence }}%
                  </p>
                  <div
                    class="w-3 h-3 rounded-full animate-pulse"
                    :class="{
                      'bg-green-500': prediction.confidence >= 80,
                      'bg-yellow-500':
                        prediction.confidence >= 50 &&
                        prediction.confidence < 80,
                      'bg-red-500': prediction.confidence < 50,
                    }"
                  ></div>
                </div>
              </div>

              <!-- Date -->
              <div
                class="bg-white rounded-lg p-4 shadow-sm border border-gray-200"
              >
                <p
                  class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1"
                >
                  Date
                </p>
                <p class="font-semibold text-gray-700 text-sm">
                  {{
                    new Date(prediction.createdAt).toLocaleDateString("en-US", {
                      year: "numeric",
                      month: "short",
                      day: "numeric",
                    })
                  }}
                </p>
              </div>
            </div>
          </div>
          <div
            @click="deletePrediction(prediction.id)"
            class="w-full py-4 rounded-xl bg-red-500 text-white text-2xl font-bold cursor-pointer hover:bg-red-400 transition-all text-center"
          >
            DELETE
          </div>
        </div>

        <!-- <div class="mr-4">
          <progress class="progress progress-accent w-60 rounded-full" value="85" max="100"></progress>
        </div> -->
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "history",
  data() {
    return {
      historyData: [],
      class_name: "",
      filtred_data: [],
      clicked: false,
    };
  },
  methods: {
    async fetchHistoryData() {
      try {
        const res = await axios.get("http://localhost:8000/predictions");

        if (res.status !== 200) return;

        const data = res.data;

        console.log("🤫🤫🤫", data);
        this.historyData = data.predictions;
      } catch (error) {
        console.log("Can;t fetch this data sure");
      }
    },
    async filtred_by_class(class_name) {
      try {
        const res = await axios.get(
          `http://localhost:8000/predictions/class/${class_name}`
        );

        if (res.status !== 200) return;

        // make the data more available and more cleaner
        const data = res.data.predictions;

        this.filtred_data = data;

        // console.log("The class are :", data);
        if (
          class_name == "Healthy" ||
          class_name == "Late blight" ||
          class_name == "Early blight"
        ) {
          this.clicked = true;
        } else {
          this.clicked = false;
        }
      } catch (error) {
        console.log("Error while uploading data");
      }
    },
    async deletePrediction(id) {
      try {
        const res = await axios.delete(
          `http://localhost:8000/predictions/${id}`
        );

        if (res.status !== 200) return;

        console.log("The operation succeeded");

        // Optionally refresh the list after deletion
        this.historyData = this.historyData.filter((p) => p.id !== id);
      } catch (error) {
        console.log("Sorry, I am not available right now:", error);
      }
    },
  },
  mounted() {
    this.fetchHistoryData();
  },
};
</script>