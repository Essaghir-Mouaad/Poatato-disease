<script>
import axios from "axios";
import { Upload } from "lucide-vue-next";
import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

export default {
  name: "UploadSection",
  components: { Upload, Bar },
  props: {
    recent: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      file: null,
      dataP: [],
      previewUrl: null,
      loading: false,
      uploaded: false,
      chartData: null,
      chartOptions: { responsive: true },
      recentChart: {
        labels: ["Confidence Level", "Miss Predicted"],
        datasets: [
          {
            label: "Prediction of recent Upload",
            backgroundColor: ["#4CAF50", "#F87171"],
            data: [],
          },
        ],
      },
    };
  },
  methods: {
    uploadFile(event) {
      const selectedFile = event.target.files[0];
      if (!selectedFile) return;

      this.file = selectedFile;
      this.uploaded = true;

      if (selectedFile.type.startsWith("image/")) {
        this.previewUrl = URL.createObjectURL(selectedFile);
      }

      console.log("📤 Selected file:", this.file.name);
    },

    async sendImage() {
      if (!this.file) {
        alert("Please select a file first!");
        return;
      }

      const formData = new FormData();
      formData.append("file", this.file);
      this.loading = true;

      try {
        const res = await axios.post(
          "http://localhost:8000/predict",
          formData,
          {
            headers: { "Content-Type": "multipart/form-data" },
          }
        );

        if (res.status !== 200) return;

        console.log("Prediction result:", res.data);
        this.dataP.push(res.data);

        // Update chart data dynamically
        const confidence = res.data.confidence || 0;
        this.chartData = {
          labels: ["Confidence Level", "Miss Predicted"],
          datasets: [
            {
              label: "Prediction Confidence",
              backgroundColor: ["#4CAF50", "#F87171"],
              data: [confidence, 100 - confidence],
            },
          ],
        };
      } catch (error) {
        console.error("❌ Error while sending the image:", error);
        alert("Upload failed. Check console for details.");
      } finally {
        this.loading = false;
      }
    },
  },
  watch: {
    recent: {
      immediate: true,
      handler(newVal) {
        if (newVal && newVal.length > 0) {
          const conf = newVal[0].confidence;
          this.recentChart.datasets[0].data = [conf, 100 - conf];
        }
      },
    },
  },
};
</script>

<template>
  <div class="min-h-screen bg-gray-100 p-8">
    <h1 class="text-4xl font-bold text-gray-800 mb-6">Make a Prediction</h1>
    <p class="text-gray-600 mb-6">Upload your file for analysis:</p>

    <!-- The Upload section -->
    <div
      class="grid grid-cols-1 lg:grid-cols-2 w-full gap-6 p-6 bg-gradient-to-br from-blue-50 to-indigo-50 rounded-3xl shadow-xl border-l-4 border-blue-300">
      <!-- Upload Section -->
      <div
        class="bg-white rounded-2xl shadow-lg p-8 flex flex-col items-center space-y-6 hover:shadow-2xl transition-shadow duration-300">
        <input type="file" id="fileUpload" @change="uploadFile" accept=".pdf,.jpg,.jpeg,.png" hidden />

        <!-- Upload Area -->
        <label v-if="!uploaded" for="fileUpload"
          class="flex flex-col items-center justify-center w-full h-64 border-3 border-dashed border-blue-300 rounded-2xl cursor-pointer hover:border-blue-500 hover:bg-blue-50 transition-all duration-300 group">
          <Upload
            class="w-16 h-16 text-blue-400 mb-3 group-hover:text-blue-600 group-hover:scale-110 transition-transform duration-300" />
          <span class="text-gray-600 text-base font-medium group-hover:text-blue-600">Click or drag a file to
            upload</span>
          <span class="text-gray-400 text-sm mt-2">PDF, JPG, JPEG, PNG supported</span>
        </label>

        <!-- Image Preview -->
        <div v-if="previewUrl" class="w-full flex flex-col items-center space-y-4">
          <div class="relative group">
            <img :src="previewUrl" alt="Preview"
              class="rounded-2xl shadow-xl w-64 h-64 object-cover border-4 border-blue-100 group-hover:border-blue-300 transition-all duration-300" />
            <div
              class="absolute inset-0 bg-black bg-opacity-0 group-hover:bg-opacity-10 rounded-2xl transition-all duration-300">
            </div>
          </div>

          <p class="text-gray-700 text-base font-medium text-center bg-gray-50 px-4 py-2 rounded-lg">
            <span class="text-gray-500">Selected:</span>
            <span class="font-bold text-blue-600">{{ file.name }}</span>
          </p>
        </div>

        <!-- Upload Button -->
        <button @click="sendImage" :disabled="loading || !file"
          class="w-full bg-gradient-to-r from-blue-600 to-indigo-600 text-white px-8 py-4 rounded-xl font-semibold text-lg hover:from-blue-700 hover:to-indigo-700 transform hover:scale-105 transition-all duration-300 shadow-lg disabled:opacity-50 disabled:cursor-not-allowed disabled:hover:scale-100">
          <span v-if="!loading" class="flex items-center justify-center gap-2">
            <Upload class="w-5 h-5" />
            Send File
          </span>
          <span v-else class="flex items-center justify-center gap-2">
            <svg class="animate-spin h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none"
              viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
              </path>
            </svg>
            Uploading...
          </span>
        </button>
      </div>

      <!-- Results Section -->
      <div v-if="dataP.length == 0"
        class="bg-gradient-to-br from-gray-100 to-gray-200 rounded-2xl text-center shadow-lg p-12 mx-auto flex flex-col items-center justify-center space-y-6 border-l-4 border-gray-200 hover:shadow-xl transition-shadow duration-300 w-full">
        <div class="text-6xl animate-pulse">📊</div>
        <h3 class="text-3xl font-bold text-gray-700">Awaiting Prediction</h3>
        <p class="text-gray-500 text-lg">Upload a file to see prediction results</p>
        <div class="flex gap-2 mt-4">
          <span class="text-4xl animate-bounce" style="animation-delay: 0s">🤫</span>
          <span class="text-4xl animate-bounce" style="animation-delay: 0.1s">🤫</span>
          <span class="text-4xl animate-bounce" style="animation-delay: 0.2s">🤫</span>
        </div>
      </div>

      <!-- Prediction Results -->
      <div v-else
        class="bg-white rounded-2xl shadow-lg p-8 mx-auto flex flex-col space-y-6 border-l-8 border-green-500 hover:shadow-2xl transition-shadow duration-300">
        <!-- Title with icon -->
        <div class="flex items-center gap-3">
          <div
            class="w-12 h-12 bg-gradient-to-br from-green-400 to-blue-500 rounded-xl flex items-center justify-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-white" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
          <p class="text-3xl font-bold text-gray-800">Prediction Results</p>
        </div>

        <!-- Predicted Class Card -->
        <div class="bg-gradient-to-r from-green-50 to-emerald-50 rounded-xl p-6 border-2 border-green-200">
          <p class="text-sm font-semibold text-gray-500 uppercase tracking-wide mb-2">Predicted Class</p>
          <p class="text-4xl font-bold text-green-600">{{ dataP[0].predicted_class || 'Unknown' }}</p>
        </div>

        <!-- Confidence Score -->
        <div class="bg-gradient-to-r from-blue-50 to-indigo-50 rounded-xl p-6 border-2 border-blue-200">
          <p class="text-sm font-semibold text-gray-500 uppercase tracking-wide mb-2">Confidence Score</p>
          <div class="flex items-end gap-2">
            <p class="text-5xl font-bold text-blue-600">{{ dataP[0].confidence }}%</p>
          </div>
          <!-- Progress bar -->
          <div class="w-full bg-gray-200 rounded-full h-3 mt-4 overflow-hidden">
            <div class="h-full rounded-full transition-all duration-1000 ease-out" :class="{
              'bg-gradient-to-r from-green-400 to-green-600': dataP[0].confidence >= 80,
              'bg-gradient-to-r from-yellow-400 to-yellow-600': dataP[0].confidence >= 50 && dataP[0].confidence < 80,
              'bg-gradient-to-r from-red-400 to-red-600': dataP[0].confidence < 50
            }" :style="{ width: dataP[0].confidence + '%' }"></div>
          </div>
        </div>

        <!-- Confidence Message with dynamic styling -->
        <div class="rounded-xl p-5 border-2" :class="{
          'bg-green-50 border-green-300': dataP[0].confidence >= 80,
          'bg-yellow-50 border-yellow-300': dataP[0].confidence >= 50 && dataP[0].confidence < 80,
          'bg-red-50 border-red-300': dataP[0].confidence < 50
        }">
          <!-- High Confidence -->
          <div v-if="dataP[0].confidence >= 80" class="flex items-start gap-3">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-green-600 flex-shrink-0 mt-0.5" fill="none"
              viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <div>
              <p class="text-green-700 font-bold text-lg">Excellent Prediction!</p>
              <p class="text-green-600 mt-1">The model is highly confident with {{ dataP[0].confidence }}% accuracy.</p>
            </div>
          </div>

          <!-- Medium Confidence -->
          <div v-else-if="dataP[0].confidence >= 50" class="flex items-start gap-3">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-yellow-600 flex-shrink-0 mt-0.5" fill="none"
              viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
            </svg>
            <div>
              <p class="text-yellow-700 font-bold text-lg">Good Prediction</p>
              <p class="text-yellow-600 mt-1">Confidence is decent at {{ dataP[0].confidence }}%. Consider reviewing the
                result.</p>
            </div>
          </div>

          <!-- Low Confidence -->
          <div v-else class="flex items-start gap-3">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-red-600 flex-shrink-0 mt-0.5" fill="none"
              viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <div>
              <p class="text-red-700 font-bold text-lg">Low Confidence</p>
              <p class="text-red-600 mt-1">Prediction confidence is {{ dataP[0].confidence }}%. Manual review
                recommended.</p>
            </div>
          </div>
        </div>

        <!-- Additional Info -->
        <div class="bg-gray-50 rounded-xl p-5 border-2 border-gray-200">
          <div class="flex items-start gap-3">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-500 flex-shrink-0 mt-0.5" fill="none"
              viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            <p class="text-gray-600 leading-relaxed">
              This result is based on our model's analysis of the uploaded file. Always double-check important
              predictions.
            </p>
          </div>
        </div>
      </div>
    </div>

    <div 
  class="w-full bg-white rounded-2xl shadow-xl overflow-hidden border-l-4 border-indigo-300 my-8" 
  v-if="!chartData && recent && recent.length > 0"
>
  <!-- Header -->
  <div class="bg-gradient-to-r from-indigo-400 to-purple-500 px-6 py-4">
    <h2 class="text-2xl font-bold text-white flex items-center gap-3">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      Last Upload
    </h2>
  </div>

  <!-- Content Card -->
  <div class="p-6">
    <div class="bg-gradient-to-br from-gray-50 to-indigo-50 rounded-xl p-6 border-2 border-indigo-100 hover:border-indigo-300 transition-all duration-300">
      <div class="flex items-center gap-6">
        <!-- Image with enhanced styling -->
        <div class="relative group flex-shrink-0">
          <img 
            :src="recent[0].image_url" 
            alt="Prediction Image"
            class="w-28 h-28 md:w-32 md:h-32 object-cover rounded-2xl border-4 border-white shadow-lg group-hover:scale-105 transition-transform duration-300" 
          />
          <div class="absolute inset-0 bg-black bg-opacity-0 group-hover:bg-opacity-10 rounded-2xl transition-all duration-300"></div>
        </div>

        <!-- Info Grid -->
        <div class="flex-1 grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Filename -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-gray-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">File</p>
            <p class="font-bold text-gray-800 truncate text-sm">{{ recent[0].filename }}</p>
          </div>

          <!-- Predicted Class -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-green-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Class</p>
            <p class="font-bold text-green-600 text-lg">{{ recent[0].predicted_class }}</p>
          </div>

          <!-- Confidence -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-blue-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Confidence</p>
            <div class="flex items-center gap-2">
              <p class="font-bold text-blue-600 text-2xl">{{ recent[0].confidence }}%</p>
              <div 
                class="w-3 h-3 rounded-full animate-pulse"
                :class="{
                  'bg-green-500': recent[0].confidence >= 80,
                  'bg-yellow-500': recent[0].confidence >= 50 && recent[0].confidence < 80,
                  'bg-red-500': recent[0].confidence < 50
                }"
              ></div>
            </div>
          </div>

          <!-- Date -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-gray-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Date</p>
            <p class="font-semibold text-gray-700 text-sm">
              {{ new Date(recent[0].createdAt).toLocaleDateString('en-US', { 
                year: 'numeric', 
                month: 'short', 
                day: 'numeric' 
              }) }}
            </p>
          </div>
        </div>
      </div>
    </div>

    <!-- Chart Section -->
    <div class="mt-6 bg-white rounded-xl p-6 border-2 border-gray-200 shadow-sm">
      <h3 class="text-lg font-bold text-gray-700 mb-4 flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
        </svg>
        Confidence Analysis
      </h3>
      <Bar :data="recentChart" :options="chartOptions" />
    </div>
  </div>
</div>

    <!-- Display after the image loaded -->
    <div v-if="chartData" class="mt-10 bg-white p-6 rounded-xl shadow-md">
      <h2 class="text-2xl font-semibold text-gray-800 mb-4">
        Prediction Confidence
      </h2>

      <!-- Create Horizontal bar contain info -->
      <div class="flex items-center gap-4 w-full p-4 my-10">
        <img :src="dataP[0].image_url" alt="Prediction Image"
                      class="w-28 h-28 md:w-32 md:h-32 object-cover rounded-2xl border-4 border-white shadow-lg group-hover:scale-105 transition-transform duration-300" />

        <div class="flex-1 grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Filename -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-gray-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">File</p>
            <p class="font-bold text-gray-800 truncate text-sm">{{ dataP[0].filename }}</p>
          </div>

          <!-- Predicted Class -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-green-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Class</p>
            <p class="font-bold text-green-600 text-lg">{{ dataP[0].predicted_class }}</p>
          </div>

          <!-- Confidence -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-blue-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Confidence</p>
            <div class="flex items-center gap-2">
              <p class="font-bold text-blue-600 text-2xl">{{ dataP[0].confidence }}%</p>
              <div 
                class="w-3 h-3 rounded-full animate-pulse"
                :class="{
                  'bg-green-500': dataP[0].confidence >= 80,
                  'bg-yellow-500': dataP[0].confidence >= 50 && dataP[0].confidence < 80,
                  'bg-red-500': dataP[0].confidence < 50
                }"
              ></div>
            </div>
          </div>

          <!-- Date -->
          <div class="bg-white rounded-lg p-4 shadow-sm border border-gray-200">
            <p class="text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1">Date</p>
            <p class="font-semibold text-gray-700 text-sm">
              {{ new Date(dataP[0].createdAt).toLocaleDateString('en-US', { 
                year: 'numeric', 
                month: 'short', 
                day: 'numeric' 
              }) }}
            </p>
          </div>
        </div>
      </div>

      <!-- Predicted chartJS -->
      <Bar :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>
