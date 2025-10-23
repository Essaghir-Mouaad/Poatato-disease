<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 p-4 md:p-8">
    <!-- Header -->
    <div class="max-w-7xl mx-auto mb-8">
      <h1 class="text-3xl md:text-4xl font-bold text-gray-800 mb-2">
        Prediction Analysis
      </h1>
      <p class="text-gray-600">
        Visual representation of your classification results
      </p>
    </div>

    <!-- Main Content -->
    <div class="max-w-7xl mx-auto space-y-6">
      
      <!-- Summary Card -->
      <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-gray-700 mb-1">
              Latest Analysis
            </h2>
            <p class="text-sm text-gray-500">Date: 2025-10-20</p>
          </div>
          <div class="px-4 py-2 bg-blue-50 text-blue-600 rounded-lg text-sm font-medium">
            {{ count_data.length }} Classes
          </div>
        </div>
      </div>

      <!-- Charts Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        
        <!-- Bar Chart -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-700">Bar Chart</h3>
            <p class="text-sm text-gray-500">Comparison view</p>
          </div>
          <div class="h-64">
            <Bar :data="Chart_Setings" :options="barChartOptions" />
          </div>
        </div>

        <!-- Pie Chart -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-700">Distribution</h3>
            <p class="text-sm text-gray-500">Percentage breakdown</p>
          </div>
          <div class="h-64 flex items-center justify-center">
            <Pie :data="Chart_Setings" :options="pieChartOptions" />
          </div>
        </div>

        <!-- Line Chart -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-700">Trend Analysis</h3>
            <p class="text-sm text-gray-500">Linear representation</p>
          </div>
          <div class="h-64">
            <Line :data="Chart_Setings" :options="lineChartOptions" />
          </div>
        </div>

        <!-- Polar Area Chart -->
        <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-700">Polar View</h3>
            <p class="text-sm text-gray-500">Radial distribution</p>
          </div>
          <div class="h-64 flex items-center justify-center">
            <PolarArea :data="Chart_Setings" :options="polarChartOptions" />
          </div>
        </div>

      </div>

      <!-- Data Table (Optional) -->
      <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-6">
        <h3 class="text-lg font-semibold text-gray-700 mb-4">Data Summary</h3>
        <div class="overflow-x-auto">
          <table class="w-full text-sm">
            <thead>
              <tr class="border-b border-gray-200">
                <th class="text-left py-3 px-4 font-semibold text-gray-700">Class</th>
                <th class="text-right py-3 px-4 font-semibold text-gray-700">Count</th>
                <th class="text-right py-3 px-4 font-semibold text-gray-700">Percentage</th>
              </tr>
            </thead>
            <tbody>
              <tr 
                v-for="(item, index) in count_data" 
                :key="index"
                class="border-b border-gray-100 hover:bg-gray-50 transition"
              >
                <td class="py-3 px-4 font-medium text-gray-800">
                  {{ item.class_name }}
                </td>
                <td class="py-3 px-4 text-right text-gray-600">
                  {{ item.count }}
                </td>
                <td class="py-3 px-4 text-right text-gray-600">
                  {{ calculatePercentage(item.count) }}%
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import { Bar, Pie, Line, PolarArea } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  LineElement,
  PointElement,
  RadialLinearScale,
  CategoryScale,
  LinearScale,
  Filler,
  ArcElement
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  LineElement,
  ArcElement,
  PointElement,
  RadialLinearScale,
  CategoryScale,
  LinearScale,
  Filler
);

export default {
  name: 'Analysis',
  components: {
    Bar, 
    Pie, 
    Line, 
    PolarArea
  },
  props: {
    count_data: {
      type: Array,
      default: () => [],
    }
  },
  data() {
    return {
      Chart_Setings: {
        labels: [],
        datasets: [
          {
            label: "Predictions per Class",
            data: [],
            backgroundColor: ["#4ade80", "#f87171", "#facc15"],
            borderColor: ["#22c55e", "#ef4444", "#eab308"],
            borderWidth: 2,
          },
        ],
      }
    }
  },
  computed: {
    baseChartOptions() {
      return {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: true,
            position: 'bottom',
            labels: {
              padding: 15,
              font: {
                size: 12
              }
            }
          }
        }
      }
    },
    barChartOptions() {
      return {
        ...this.baseChartOptions,
        scales: {
          y: {
            beginAtZero: true,
            grid: {
              color: '#f3f4f6'
            }
          },
          x: {
            grid: {
              display: false
            }
          }
        }
      }
    },
    lineChartOptions() {
      return {
        ...this.baseChartOptions,
        scales: {
          y: {
            beginAtZero: true,
            grid: {
              color: '#f3f4f6'
            }
          },
          x: {
            grid: {
              display: false
            }
          }
        },
        elements: {
          line: {
            tension: 0.4
          }
        }
      }
    },
    pieChartOptions() {
      return {
        ...this.baseChartOptions,
        plugins: {
          ...this.baseChartOptions.plugins,
          legend: {
            ...this.baseChartOptions.plugins.legend,
            position: 'right'
          }
        }
      }
    },
    polarChartOptions() {
      return {
        ...this.baseChartOptions,
        scales: {
          r: {
            beginAtZero: true,
            grid: {
              color: '#f3f4f6'
            }
          }
        }
      }
    }
  },
  watch: {
    count_data: {
      immediate: true,
      handler(newVal) {
        if (newVal && newVal.length > 0) {
          this.Chart_Setings = {
            labels: newVal.map(p => p.class_name),
            datasets: [
              {
                label: "Predictions per Class",
                data: newVal.map(p => p.count),
                backgroundColor: ["#4ade80", "#f87171", "#facc15"],
                borderColor: ["#22c55e", "#ef4444", "#eab308"],
                borderWidth: 2,
              },
            ],
          }
        }
      }
    }
  },
  methods: {
    calculatePercentage(count) {
      const total = this.count_data.reduce((sum, item) => sum + item.count, 0);
      return total > 0 ? ((count / total) * 100).toFixed(1) : 0;
    }
  }
}
</script>