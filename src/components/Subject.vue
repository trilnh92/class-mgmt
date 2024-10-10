<script setup>
import { ref } from "vue";
import { PencilIcon, TrashIcon, PlusIcon } from "@heroicons/vue/24/solid";

import mathScoreData from "../assets/data/mathScore.json";

const mathScores = ref(mathScoreData);

function calcAverage(item) {
  return parseFloat(
    item.quizz * 0.2 + item.midTerm * 0.4 + item.final * 0.4
  ).toFixed(2);
}
</script>

<template>
  <section class="header-wrapper">
    <h1>Math Score Management</h1>
  </section>

  <section class="subject-wrapper">
    <div class="overflow-x-auto">
      <table class="table">
        <thead>
          <tr>
            <th>#</th>
            <th class="hidden">ID</th>
            <th>Name</th>
            <th>Quizz</th>
            <th>Mid-Term</th>
            <th>Final</th>
            <th class="average">Average</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in mathScores" :key="item.id">
            <td>{{ index + 1 }}</td>
            <td class="hidden">{{ item.id }}</td>
            <td>{{ item.student_name }}</td>
            <td>{{ item.quizz }}</td>
            <td>{{ item.midTerm }}</td>
            <td>{{ item.final }}</td>
            <td class="average">{{ calcAverage(item) }}</td>
            <td>
              <button class="btn btn-circle">
                <pencil-icon class="icon size-5 text-green-500" />
              </button>
              <button class="btn btn-circle">
                <trash-icon class="icon size-5 text-red-500" />
              </button>
            </td>
          </tr>
          <tr>
            <td></td>
            <td class="hidden"></td>
            <td>
              <input
                type="text"
                placeholder="Input Student Name"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                placeholder="Input Quizz Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                placeholder="Input Mid-Term Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                placeholder="Input Final Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td class="average"></td>
            <td>
              <button class="btn btn-circle">
                <plus-icon class="icon size-5 text-green-500" />
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>


<style lang="scss">
body {
  min-height: 100vh;
}
.header-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 80px;
}

.subject-wrapper {
  max-width: 1366px;
  margin-inline: auto;

  padding-top: 60px;
}

.average {
  color: #f41f7f;
  font-weight: 700;
}

.table {
  th,
  td {
    font-size: 16px;
  }

  tr {
    border-bottom-color: #272727;
  }
}
</style>