<script setup>
import { ref, onMounted } from "vue";
import {
  PencilIcon,
  TrashIcon
} from "@heroicons/vue/24/solid";
import { v4 as uuid } from "uuid";
import mathScoreData from "../assets/data/mathScore.json";
//
import { useForm } from "vee-validate";

import { toTypedSchema } from "@vee-validate/yup";
import * as yup from "yup";

//
const mathScores = ref(mathScoreData);
//
const modalTitle = ref("");
const isShowModal = ref(false);
const showRemoveForm = ref(false);
//
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

function addStudent(data) {
  data.id = uuid();

  mathScores.value.push(Object.assign({}, data));

  closeModal();
}

function updateStudent(student) {
  // console.log("update student", student);
  //
  const index = mathScores.value.findIndex(
    (target) => target.id === student.id
  );
  //
  index !== -1 ? (mathScores.value[index].isModifyForm = true) : "";
  //
  mathScores.value.splice(index, 1, student);
  //
  // console.log("UPDATE AFTER", mathScores.value);

  closeModal();
}

function deleteStudent(student) {
  console.log("remove student", student);
  //
  const index = mathScores.value.findIndex(
    (target) => target.id === student.id
  );
  index !== -1 ? mathScores.value.splice(index, 1) : "";
  //
  console.log("DELETE AFTER", mathScores.value);
  //
  showRemoveForm.value = false;
  closeModal();
}

function openModal(action, data) {
  // console.log("Open Modal");

  const obj = data ? JSON.parse(JSON.stringify(data, null, 2)) : "";

  if (action === "add") {
    modalTitle.value = "Add New Student";
  } else if (action === "update") {
    modalTitle.value = "Update Student";
    setValues(obj);
  } else {
    modalTitle.value = "Remove Student";

    showRemoveForm.value = true;
    setValues(obj);
  }

  isShowModal.value = true;
}

function closeModal() {
  isShowModal.value = false;
  showRemoveForm.value = false;
  resetForm();
}

const { errors, handleSubmit, defineField, resetForm, setValues } = useForm({
  validationSchema: toTypedSchema(
    yup.object({
      id: yup.string().default(""),
      name: yup.string().required("Student Name is required").default(""),
      quizz: yup
        .number()
        .typeError("Student Quizz must be a number")
        .transform((val) => Number(val))
        .required("Student Quizz is required")
        .min(0, "Student Quizz must be at least 0")
        .max(10, "Student Quizz must be no more than 10")
        .default(0),
      midTerm: yup
        .number()
        .typeError("Student Mid Term must be a number")
        .transform((val) => Number(val))
        .required("Student Mid Term is required")
        .min(0, "Student Mid Term must be at least 0")
        .max(10, "Student Mid Term must be no more than 10")
        .default(0),
      final: yup
        .number()
        .typeError("Student Final must be a number")
        .transform((val) => Number(val))
        .required("Student Final is required")
        .min(0, "Student Final must be at least 0")
        .max(10, "Student Final must be no more than 10")
        .default(0),
    })
  ),
});
const [id, idAttrs] = defineField("id");
const [name, nameAttrs] = defineField("name");
const [quizz, quizzAttrs] = defineField("quizz");
const [midTerm, midTermAttrs] = defineField("midTerm");
const [final, finalAttrs] = defineField("final");

const onSubmit = handleSubmit((values) => {
  // console.log("Submit", values, JSON.stringify(values, null, 2));

  let data = JSON.parse(JSON.stringify(values, null, 2));

  if (data.id === "") {
    // console.log("Add");
    addStudent(data);
  } else {
    if (!showRemoveForm.value) {
      // console.log("Update");
      updateStudent(data);
    } else {
      // console.log("Remove");
      deleteStudent(data);
    }
  }
});
</script>

<template>
  <!-- Modal -->
  <dialog id="studentModal" class="modal" open v-if="isShowModal">
    <div class="modal-box">
      <h3 class="text-lg font-bold">{{ modalTitle }}</h3>
      <div class="modal-action">
        <form method="dialog" @submit="onSubmit">
          <div class="felx w-full" v-if="!showRemoveForm">
            <input v-model="id" v-bind="idAttrs" type="text" v-show="false" />

            <label class="form-control w-full">
              <div class="label">
                <span class="label-text">Student Name</span>
              </div>
              <input
                type="text"
                placeholder="Enter Student Name"
                class="input input-bordered w-full max-w-xs"
                :class="{ 'input-error': errors.name }"
                v-model="name"
                v-bind="nameAttrs"
              />
              <p class="error-message">{{ errors.name }}</p>
            </label>
            <div class="divider"></div>
            <label class="form-control w-full max-w-xs">
              <div class="label">
                <span class="label-text">Quizz Score</span>
              </div>

              <input
                type="text"
                placeholder="Enter Quizz Score"
                class="input input-bordered w-full max-w-xs"
                :class="{ 'input-error': errors.quizz }"
                v-model="quizz"
                v-bind="quizzAttrs"
              />

              <p class="error-message">{{ errors.quizz }}</p>
            </label>
            <div class="divider"></div>
            <label class="form-control w-full max-w-xs">
              <div class="label">
                <span class="label-text">Mid-term Score</span>
              </div>
              <input
                type="text"
                placeholder="Enter Mid-term Score"
                class="input input-bordered w-full max-w-xs"
                v-model="midTerm"
                v-bind="midTermAttrs"
                :class="{ 'input-error': errors.midTerm }"
              />
              <p class="error-message">{{ errors.midTerm }}</p>
            </label>
            <div class="divider"></div>
            <label class="form-control w-full max-w-xs">
              <div class="label">
                <span class="label-text">Final Score</span>
              </div>
              <input
                type="text"
                placeholder="Enter Final Score"
                class="input input-bordered w-full max-w-xs"
                v-model="final"
                v-bind="finalAttrs"
                :class="{ 'input-error': errors.final }"
              />
              <p class="error-message">{{ errors.final }}</p>
            </label>
            <div class="divider"></div>
            <label class="form-control w-full max-w-xs">
              <div class="label">
                <span class="label-text">Average</span>
              </div>
              <h2 class="card-title">
                {{
                  parseFloat(quizz * 0.2 + midTerm * 0.4 + final * 0.4).toFixed(
                    2
                  )
                }}
              </h2>
            </label>
            <div class="divider"></div>
            <button type="submit" class="btn btn-secondary">Save</button>
            <button class="btn ml-4" @click="closeModal()">Cancel</button>
          </div>
          <div class="felx w-full" v-else>
            <div class="divider"></div>
            <h2>
              Are you sure to remove this student:
              <b class="bg-secondary">{{ name }}</b> ?
            </h2>
            <div class="divider"></div>
            <button type="submit" class="btn btn-secondary">Yes</button>
            <button class="btn ml-4" @click="closeModal()">No</button>
          </div>
        </form>
      </div>
    </div>
  </dialog>

  <!-- Header -->
  <section class="header-wrapper">
    <h1>Math Score Management</h1>
  </section>

  <!-- List -->
  <section class="subject-wrapper">
    <div class="overflow-x-auto">
      <button
        class="btn btn-secondary mb-4 mr-4 float-right"
        @click="openModal('add', '')"
      >
        Add New Student
      </button>
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
            <td>{{ item.name }}</td>
            <td>
              {{ item.quizz }}
            </td>
            <td>
              {{ item.midTerm }}
            </td>
            <td>{{ item.final }}</td>
            <td class="average">{{ calcAverage(item) }}</td>
            <td>
              <button class="btn btn-circle" @click="openModal('update', item)">
                <pencil-icon class="icon size-5 text-green-500" />
              </button>
              <button class="btn btn-circle" @click="openModal('remove', item)">
                <trash-icon class="icon size-5 text-red-500" />
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

.modal-action {
  width: 100%;
  justify-content: center;
  align-items: center;

  form {
    width: 100%;
  }

  .error-message {
    margin: 0;
    padding: 0;
    padding-block: 15px 0;

    font-size: 14px;
    font-style: italic;
    color: #ff6f6f;
  }
}
</style>