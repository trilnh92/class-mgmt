<script setup>
import { ref, onMounted } from "vue";
import {
  PencilIcon,
  TrashIcon,
  PlusIcon,
  CheckIcon,
  XMarkIcon,
} from "@heroicons/vue/24/solid";
import { v4 as uuid } from "uuid";
import mathScoreData from "../assets/data/mathScore.json";

const mathScores = ref(mathScoreData);

const student = ref({
  id: "",
  name: "",
  quizz: "",
  midTerm: "",
  final: "",
});

const isModifyForm = ref(false);

const isUpdate = ref(false);
const isRemove = ref(false);

const userAction = ref();

function calcAverage(item) {
  return item
    ? parseFloat(
        item.quizz * 0.2 + item.midTerm * 0.4 + item.final * 0.4
      ).toFixed(2)
    : 0;
}

onMounted(() => {
  mathScores.value.forEach((item) => {
    item.isModifyForm = false;
  });
});

function addStudent() {
  student.value.id = uuid();
  mathScores.value.push(Object.assign({}, student.value));

  // Reset

  student.value.id = "";
  student.value.name = "";
  student.value.quizz = "";
  student.value.midTerm = "";
  student.value.final = "";

  console.log(student.value, mathScores.value);
}

function modilfyStudent(item) {
  console.log("modilfy student", item);

  userAction.value = "update";
  //
  const index = mathScores.value.findIndex((target) => target.id === item.id);
  index !== -1 ? (mathScores.value[index].isModifyForm = true) : "";
  //
}

function updateStudent(student) {
  console.log("update student", student);
  //
  const index = mathScores.value.findIndex(
    (target) => target.id === student.id
  );
  index !== -1 ? (mathScores.value[index].isModifyForm = true) : "";
  //
  mathScores.value.splice(index, 1, student);
  mathScores.value[index].isModifyForm = false;
  //
  console.log("UPDATE AFTER", mathScores.value);
}

function deleteStudent(studentId) {
  console.log("remove student", studentId);
  //
  const index = mathScores.value.findIndex((target) => target.id === studentId);
  index !== -1 ? mathScores.value.splice(index, 1) : "";
  //
  console.log("DELETE AFTER", mathScores.value);
}

function confirm(student) {
  if (userAction.value === "update") {
    updateStudent(student);
  }
  //
  userAction.value = "";
}

function cancel(item) {
  console.log("Cancel");
  //
  userAction.value = "";
  item.isModifyForm = false;
}
</script>

<template>
  <section class="header-wrapper">
    <h1>Math Score Management</h1>
  </section>

  <h3>{{ mathScores.length }}</h3>

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
            <td>
              {{ index + 1 }}
            </td>
            <td class="hidden">{{ item.id }}</td>
            <td>
              <input
                v-if="item.isModifyForm"
                type="text"
                v-model="item.name"
                placeholder="Input Student Name"
                class="input input-bordered w-full max-w-xs"
              />
              <span v-else>{{ item.name }}</span>
            </td>
            <td>
              <input
                v-if="item.isModifyForm"
                type="text"
                v-model="item.quizz"
                placeholder="Input Quizz Score"
                class="input input-bordered w-full max-w-xs"
              />
              <span v-else>{{ item.quizz }}</span>
            </td>
            <td>
              <input
                v-if="item.isModifyForm"
                type="text"
                v-model="item.midTerm"
                placeholder="Input Mid-Term Score"
                class="input input-bordered w-full max-w-xs"
              />
              <span v-else>{{ item.midTerm }}</span>
            </td>
            <td>
              <input
                v-if="item.isModifyForm"
                type="text"
                v-model="item.final"
                placeholder="Input Final Score"
                class="input input-bordered w-full max-w-xs"
              />
              <span v-else>{{ item.final }}</span>
            </td>
            <td class="average">{{ calcAverage(item) }}</td>
            <td v-if="!item.isModifyForm">
              <button class="btn btn-circle" @click="modilfyStudent(item)">
                <pencil-icon class="icon size-5 text-green-500" />
              </button>
              <button class="btn btn-circle" @click="deleteStudent(item.id)">
                <trash-icon class="icon size-5 text-red-500" />
              </button>
            </td>
            <td v-else>
              <button class="btn btn-circle" @click="confirm(item)">
                <check-icon class="icon size-5 text-green-500" />
              </button>
              <button class="btn btn-circle" @click="cancel(item)">
                <x-mark-icon class="icon size-5 text-red-500" />
              </button>
            </td>
          </tr>
          <tr v-if="mathScores.length === 0">
            <td colspan="7">
              <div role="alert" class="alert">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  fill="none"
                  viewBox="0 0 24 24"
                  class="stroke-info h-6 w-6 shrink-0"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                  ></path>
                </svg>
                <span>No available students.</span>
              </div>
            </td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td></td>
            <td class="hidden"></td>
            <td>
              <input
                type="text"
                v-model="student.name"
                placeholder="Input Student Name"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                v-model="student.quizz"
                placeholder="Input Quizz Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                v-model="student.midTerm"
                placeholder="Input Mid-Term Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td>
              <input
                type="text"
                v-model="student.final"
                placeholder="Input Final Score"
                class="input input-bordered w-full max-w-xs"
              />
            </td>
            <td class="average">{{ calcAverage(student) }}</td>
            <td>
              <button class="btn btn-circle" @click="addStudent()">
                <plus-icon class="icon size-5 text-green-500" />
              </button>
            </td>
          </tr>
        </tfoot>
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
  max-width: 75vw;
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

  tfoot {
    border-top: 1px solid #272727;
  }
}
</style>