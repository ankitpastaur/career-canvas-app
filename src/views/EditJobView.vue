<script setup>
import { reactive, onMounted } from "vue";
import router from "@/router";
import axios from "axios";
import { useToast } from "vue-toastification";
import { useRoute } from "vue-router";

const route = useRoute();

const jobId = route.params.id;

const toast = useToast();

const form = reactive({
  type: "Full Time",
  title: "",
  description: "",
  salary: "",
  location: "India",
  company: {
    name: "",
    description: "",
    contactEmail: "",
    contactPhone: "",
  },
});

const state = reactive({
  job: {},
  isLoading: true,
});

const handleSubmit = async () => {
  const updatedJob = {
    type: form.type,
    title: form.title,
    salary: form.salary,
    location: form.location,
    description: form.description,
    company: {
      name: form.company.name,
      description: form.company.description,
      contactEmail: form.company.contactEmail,
      contactPhone: form.company.contactPhone,
    },
  };

  try {
    const response = await axios.put(`/api/job/${jobId}`, updatedJob);
    toast.success("Job Updated successfully");
    router.push(`/jobs/${response.data.id}`);
  } catch (error) {
    console.error("Error Updating job", error);
    toast.error("Job was not added");
  }
};

onMounted(async () => {
  try {
    const response = await axios.get(`/api/job/${jobId}`);
    const jobData = response.data;

    // Check if jobData exists and has the expected structure
    if (jobData && jobData.type) {
      state.job = jobData;

      // Populate inputs only if jobData is correctly retrieved
      form.type = state.job.type || form.type;
      form.title = state.job.title || form.title;
      form.description = state.job.description || form.description;
      form.salary = state.job.salary || form.salary;
      form.location = state.job.location || form.location;
      form.company.name = state.job.company?.name || form.company.name;
      form.company.description =
        state.job.company?.description || form.company.description;
      form.company.contactEmail =
        state.job.company?.contactEmail || form.company.contactEmail;
      form.company.contactPhone =
        state.job.company?.contactPhone || form.company.contactPhone;
    } else {
      throw new Error("Job data is not in the expected format");
    }
  } catch (error) {
    console.error("Error fetching job", error);
  } finally {
    state.isLoading = false;
  }
  console.log(state.job.salary);
});
</script>
<template>
  <section class="bg-blue-50">
    <div class="container m-auto max-w-2xl py-24">
      <div
        class="bg-white px-6 py-8 mb-4 shadow-md rounded-md border m-4 md:m-0"
      >
        <form @submit.prevent="handleSubmit">
          <h2 class="text-3xl text-center font-semibold mb-6">Edit Job</h2>

          <div class="mb-4">
            <label for="type" class="block text-gray-700 font-bold mb-2"
              >Job Type</label
            >
            <select
              v-model="form.type"
              id="type"
              name="type"
              class="border rounded w-full py-2 px-3"
              required
            >
              <option value="Full-Time">Full-Time</option>
              <option value="Part-Time">Part-Time</option>
              <option value="Remote">Remote</option>
              <option value="Internship">Internship</option>
            </select>
          </div>

          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2"
              >Job Listing Name</label
            >
            <input
              type="text"
              v-model="form.title"
              id="name"
              name="name"
              class="border rounded w-full py-2 px-3 mb-2"
              placeholder="eg. Beautiful Apartment In Miami"
              required
            />
          </div>
          <div class="mb-4">
            <label for="description" class="block text-gray-700 font-bold mb-2"
              >Description</label
            >
            <textarea
              id="description"
              v-model="form.description"
              name="description"
              class="border rounded w-full py-2 px-3"
              rows="4"
              placeholder="Add any job duties, expectations, requirements, etc"
            ></textarea>
          </div>

          <div class="mb-4">
            <label for="type" class="block text-gray-700 font-bold mb-2"
              >Salary (INR)</label
            >
            <select
              id="salary"
              v-model="form.salary"
              name="salary"
              class="border rounded w-full py-2 px-3"
              required
            >
              <option value="Under ₹5L">Under ₹5L</option>
              <option value="₹5L - ₹7L">₹5L - ₹7L</option>
              <option value="₹7L - ₹10L">₹7L - ₹10L</option>
              <option value="₹10L - ₹15L">₹10L - ₹15L</option>
              <option value="₹15L - ₹20L">₹15L - ₹20L</option>
              <option value="₹20L - ₹30L">₹20L - ₹30L</option>
              <option value="Over ₹30L">Over ₹30L</option>
            </select>
          </div>

          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2"> Location </label>
            <input
              type="text"
              v-model="form.location"
              id="location"
              name="location"
              class="border rounded w-full py-2 px-3 mb-2"
              placeholder="Company Location"
              required
            />
          </div>

          <h3 class="text-2xl mb-5">Company Info</h3>

          <div class="mb-4">
            <label for="company" class="block text-gray-700 font-bold mb-2"
              >Company Name</label
            >
            <input
              type="text"
              v-model="form.company.name"
              id="company"
              name="company"
              class="border rounded w-full py-2 px-3"
              placeholder="Company Name"
            />
          </div>

          <div class="mb-4">
            <label
              for="company_description"
              class="block text-gray-700 font-bold mb-2"
              >Company Description</label
            >
            <textarea
              id="company_description"
              v-model="form.company.description"
              name="company_description"
              class="border rounded w-full py-2 px-3"
              rows="4"
              placeholder="What does your company do?"
            ></textarea>
          </div>

          <div class="mb-4">
            <label
              for="contact_email"
              class="block text-gray-700 font-bold mb-2"
              >Contact Email</label
            >
            <input
              type="email"
              v-model="form.company.contactEmail"
              id="contact_email"
              name="contact_email"
              class="border rounded w-full py-2 px-3"
              placeholder="Email address for applicants"
              required
            />
          </div>
          <div class="mb-4">
            <label
              for="contact_phone"
              class="block text-gray-700 font-bold mb-2"
              >Contact Phone</label
            >
            <input
              type="tel"
              v-model="form.company.contactPhone"
              id="contact_phone"
              name="contact_phone"
              class="border rounded w-full py-2 px-3"
              placeholder="Optional phone for applicants"
            />
          </div>

          <div>
            <button
              class="bg-blue-500 hover:bg-blue-900 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline"
              type="submit"
            >
              Update Job
            </button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>
