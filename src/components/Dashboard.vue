<template>
  <div class="history-container min-h-screen bg-gray-100 p-8">
    <!-- <predictions :recent="recent_5_uploads"/> -->

    <!-- Header -->
    <h1 class="text-5xl font-bold text-gray-800 mb-8">Dashboard</h1>

    <div
      class="grid grid-cols-1 md:grid-cols-3 gap-6 border border-current rounded-xl mt-10 p-4"
    >
      <div
        class="bg-gradient-to-r from-red-500 to-red-400 text-white rounded-2xl shadow-lg p-6 transform transition duration-300 hover:scale-105 hover:shadow-xl"
      >
        <p class="text-lg font-medium opacity-90">Number of Predictions</p>
        <p class="text-5xl font-bold mt-2">{{ total_predictions }}</p>
      </div>

      <div
        class="bg-gradient-to-r from-green-500 to-emerald-400 text-white rounded-2xl shadow-lg p-6 transform transition duration-300 hover:scale-105 hover:shadow-xl"
      >
        <p class="text-lg font-medium opacity-90">Number of Classes</p>
        <p class="text-5xl font-bold mt-2">{{ number_class }}</p>
      </div>

      <div
        class="bg-gradient-to-r from-gray-600 to-gray-400 text-white rounded-2xl shadow-lg p-6 transform transition duration-300 hover:scale-105 hover:shadow-xl"
      >
        <p class="text-lg font-medium opacity-90">Average Confidence</p>
        <p class="text-5xl font-bold mt-2">{{ average_confidence }}%</p>
      </div>
    </div>

    <h1 class="text-2xl text-gray-700 font-bold capitalize my-4">
      Recent Prediction:
    </h1>

    <!-- Display the last 5 items -->

    <div class="flex flex-col gap-4">
      <div
        v-for="prediction in recent_5_uploads"
        :key="prediction.filename"
        class="flex items-center gap-4 w-full bg-white border-l-4 border-blue-500 rounded-xl shadow hover:shadow-md transition-shadow"
      >
        <div class="flex items-center gap-4 w-full p-4 my-10">
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
                      prediction.confidence >= 50 && prediction.confidence < 80,
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

        <!-- <div class="mr-4">
          <progress class="progress progress-accent w-60 rounded-full" value="85" max="100"></progress>
        </div> -->
      </div>
    </div>

    <div class="mr-4" v-if="average_confidence">
      <h1 class="text-2xl text-gray-700 font-bold capitalize my-4">Info:</h1>
      <progress
        class="progress progress-accent w-[100%] rounded-lg"
        :value="average_confidence"
        :max="100"
      ></progress>
      <p>Confidence: {{ average_confidence }}</p>
    </div>

    <!-- <predictions :recent="recent_5_uploads"/> -->
  </div>
</template>

<script>
import Predictions from "./Predictions.vue";

export default {
  components: { Predictions },
  props: {
    average_confidence: {
      type: Number,
      default: 0,
    },
    number_class: {
      type: Number,
      default: 3,
    },
    total_predictions: {
      type: Number,
      default: 0,
    },
    recent_5_uploads: {
      type: Array,
      default: () => [],
    },
  },
};
</script>
