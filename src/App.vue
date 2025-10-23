<template>
  <div class="app-container flex w-screen h-screen">
    <!-- Sidebar -->
    <div class="h-full">
      <Sidbar @update-view="handleViewChange" />
    </div>

    <!-- Main content -->
    <div class="flex-1 h-full overflow-auto bg-gray-100">
      <Dashboard v-if="currentView === 'dashboard'" :average_confidence="average_confidence"
        :number_class="number_class" :total_predictions="total_predictions" :recent_5_uploads="recent_5_uploads" />
      <Predictions v-else-if="currentView === 'predictions'" :recent="recent_5_uploads"/>
      <Analysis v-else-if="currentView === 'analysis'"  :count_data="count_by_class"/>
      <History v-else-if="currentView === 'history'" />
      <div>Hello</div>
    </div>
  </div>
</template>

<script>
import Sidbar from "./components/Sidbar.vue";
import Dashboard from "./components/Dashboard.vue";
import Analysis from "./components/Analysis.vue";
import History from "./components/History.vue";
import Predictions from "./components/Predictions.vue";
import { ref } from "vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    Sidbar,
    Dashboard,
    Analysis,
    History,
    Predictions
  },
  data() {
    return {
      average_confidence: 0,
      number_class: 0,
      total_predictions: 0,
      recent_5_uploads: [],
      count_by_class:[],
    };
  },
  setup() {
    const currentView = ref('dashboard')

    const handleViewChange = (view) => {
      currentView.value = view
    }

    return {
      currentView,
      handleViewChange
    }
  },
   methods: {
    async fetchDashboardData() {
      try {
        const res = await fetch("http://localhost:8000/Dashboard", {
          method: "GET",
        });

        if (!res.ok) {
          console.error("Can't fetch the data from the specified URL");
          return;
        }

        const data = await res.json();

        // Update reactive variables (works like React state)
        this.average_confidence = data.average_confidence;
        this.total_predictions = data.total_predictions;
        this.number_class = 3;
        this.recent_5_uploads = data.recent_predictions;
        this.count_by_class = data.by_class;

        console.log("The data is:", data);
      } catch (error) {
        console.error("Can't finish the operation", error);
      }
    },
  },
  // Runs automatically once (like useEffect(() => {}, []))
  mounted() {
    this.fetchDashboardData();
  },
};

</script>
