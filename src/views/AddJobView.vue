<script setup>
import { reactive } from "vue";
import router from "@/router";
import axios from "axios";
import { useToast } from "vue-toastification";

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

// const handleSubmit = async () => {
//   const newJob = {
//     title: form.title,
//     type: form.type,
//     salary: form.salary,
//     location: form.location,
//     description: form.description,
//     company: {
//       name: form.company.name,
//       description: form.company.description,
//       contactEmail: form.company.contactEmail,
//       contactPhone: form.company.contactPhone,
//     },
//   };

//   try {
//     const response = await axios.post("/api/job", newJob);
//     console.log("calling response", response);

//     toast.success("Job added successfully");
//     router.push(`/jobs/${response.data.id}`);
//   } catch (error) {
//     console.error("Error fetching job", error);
//     toast.error("Job was not added");
//   }
// };

const getNextId = async () => {
  try {
    // Fetch the existing jobs to find the highest ID
    const response = await axios.get("/api/job");
    const jobs = response.data;

    // Assuming the ID is a number, find the highest numeric ID
    const maxId = jobs.reduce(
      (max, job) => Math.max(max, parseInt(job.id, 36)),
      0
    );

    // Increment and return the next ID in a base36 format (if you want alphanumeric IDs)
    return (maxId + 1).toString(36);
  } catch (error) {
    console.error("Error fetching jobs", error);
    return null; // Handle error case
  }
};

const handleSubmit = async () => {
  // Get the next sequential ID
  const nextId = await getNextId();

  if (!nextId) {
    toast.error("Unable to generate job ID");
    return;
  }

  const newJob = {
    id: nextId, // Use the generated sequential ID
    title: form.title,
    type: form.type,
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
    const response = await axios.post("/api/job", newJob);
    console.log("calling response", response);

    toast.success("Job added successfully");
    router.push(`/jobs/${newJob.id}`); // Use the newly generated ID
  } catch (error) {
    console.error("Error fetching job", error);
    toast.error("Job was not added");
  }
};
</script>

<template>
  <section class="bg-blue-50">
    <div class="container m-auto max-w-2xl py-24">
      <div
        class="bg-white px-6 py-8 mb-4 shadow-md rounded-md border m-4 md:m-0"
      >
        <form @submit.prevent="handleSubmit">
          <h2 class="text-3xl text-blue-500 text-center font-semibold mb-6">
            Add Job
          </h2>

          <!-- Job Type -->
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

          <!-- Job Title -->
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

          <!-- Job Description -->
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

          <!-- Salary in INR -->
          <div class="mb-4">
            <label for="salary" class="block text-gray-700 font-bold mb-2"
              >Salary (INR)</label
            >
            <select
              id="salary"
              v-model="form.salary"
              name="salary"
              class="border rounded w-full py-2 px-3"
              required
            >
              <option value="Under ₹3L">Under ₹3L</option>
              <option value="₹3L - ₹5L">₹3L - ₹5L</option>
              <option value="₹5L - ₹7L">₹5L - ₹7L</option>
              <option value="₹7L - ₹10L">₹7L - ₹10L</option>
              <option value="₹10L - ₹15L">₹10L - ₹15L</option>
              <option value="₹15L - ₹20L">₹15L - ₹20L</option>
              <option value="₹20L - ₹30L">₹20L - ₹30L</option>
              <option value="Over ₹30L">Over ₹30L</option>
            </select>
          </div>

          <!-- Location -->
          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2">Location</label>
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

          <!-- Company Info -->
          <h3 class="text-2xl mb-5">Company Info</h3>

          <!-- Company Name -->
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

          <!-- Company Description -->
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

          <!-- Contact Info -->
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

          <!-- Submit Button -->
          <div>
            <button
              class="bg-blue-500 hover:bg-blue-900 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline"
              type="submit"
            >
              Add Job
            </button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>
