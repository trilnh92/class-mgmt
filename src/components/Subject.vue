<script setup>
import { ref } from "vue";

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
    <table class="table-auto">
      <tr>
        <th>#</th>
        <th class="hidden">ID</th>
        <th>Name</th>
        <th>Quizz</th>
        <th>Mid-Term</th>
        <th>Final</th>
        <th class="average">Average</th>
        <th></th>
      </tr>
      <tr v-for="(item, index) in mathScores" :key="item.id">
        <td>{{ index + 1 }}</td>
        <td class="hidden">{{ item.id }}</td>
        <td>{{ item.student_name }}</td>
        <td>{{ item.quizz }}</td>
        <td>{{ item.midTerm }}</td>
        <td>{{ item.final }}</td>
        <td class="average">{{ calcAverage(item) }}</td>
        <td></td>
      </tr>
    </table>
  </section>
</template>


<style lang="scss">
.header-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 80px;
}

.subject-wrapper {
  width: 1200px;
  margin-inline: auto;

  padding-top: 60px;
}

.average {
  color: #f41f7f;
  font-weight: 700;
}
</style>