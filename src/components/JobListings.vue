<script setup>
import axios from "axios";
import { reactive, defineProps, onMounted, ref, computed } from "vue";
import JobListing from "./JobListing.vue";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

const props = defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
});

const state = reactive({
  jobs: [],
  isLoading: true,
});

// Search and sort related state
const searchQuery = ref("");
const sortOption = ref("date");

// Fetch jobs from API
onMounted(async () => {
  try {
    const response = await axios.get("/api/job");
    state.jobs = response.data;
  } catch (error) {
    console.error("Error fetching jobs", error);
  } finally {
    state.isLoading = false;
  }
});

// Filter and sort jobs
const filteredAndSortedJobs = computed(() => {
  let result = state.jobs;

  // Search filtering
  if (searchQuery.value) {
    result = result.filter((job) =>
      job.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
  }

  // Sorting
  if (sortOption.value === "date") {
    result = result.sort((a, b) => new Date(b.date) - new Date(a.date));
  } else if (sortOption.value === "title") {
    result = result.sort((a, b) => a.title.localeCompare(b.title));
  } else if (sortOption.value === "location") {
    result = result.sort((a, b) => a.location.localeCompare(b.location));
  }

  return result.slice(0, props.limit || result.length);
});
</script>

<template>
  <section class="bg-blue-50 px-4 py-10">
    <div class="container-xl lg-container m-auto">
      <h2 class="text-3xl font-bold text-blue-500 mb-6 text-center">
        Browse Jobs
      </h2>

      <div class="mb-6 text-center">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search jobs..."
          class="border px-4 py-2 rounded"
        />
        <select v-model="sortOption" class="ml-4 border px-4 py-2 rounded">
          <option value="date">Sort by Date</option>
          <option value="title">Sort by Title</option>
          <option value="location">Sort by Location</option>
        </select>
      </div>

      <!-- Show loading spinner while loading is true -->
      <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
        <PulseLoader />
      </div>

      <!-- Show job listing when done loading -->
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in filteredAndSortedJobs"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>
</template>
