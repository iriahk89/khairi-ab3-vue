<script setup>
  import { generateClient } from 'aws-amplify/api';
  import { onMounted, ref } from 'vue';
  import { get } from 'aws-amplify/api';
  import { post } from 'aws-amplify/api';

  const id = ref('');
  const name = ref('');
  const email = ref('');
  const result = ref('');
  const exams = ref([]);

  async function postExaminationsResult() {
  if (!id.value || !name.value|| !email.value|| !result.value) return;
  try {
    const restOperation = post({
      apiName: 'examinationsapi',
      path: '/examinations',
      options: {
        body: {
          StudentId: id.value,
          Name: name.value,
          EmailAddress: email.value,
          Result: result.value,
        }
      }
    });

    const { body } = await restOperation.response;
    const response = await body.json();

    console.log('POST call succeeded');
    console.log(response);

    id.value = '';
    name.value = '';
    email.value = '';
    result.value = '';
    getExaminationsResult();
  } catch (e) {
    console.log('POST call failed: ', JSON.parse(e.response.body));
  }
}

  async function getExaminationsResult() {
    try {
      const restOperation = get({ 
        apiName: 'examinationsapi',
        path: '/examinations' 
      });
      const response = await restOperation.response;
      const { body } = await restOperation.response;
      const json = await body.json();
      exams.value = json;

      console.log('GET call succeeded: ', response);
      console.log('response: ', json);
    } catch (e) {
      console.log('GET call failed: ', JSON.parse(e.response.body));
    }
  }

  onMounted(() => {
    getExaminationsResult();
  });
</script>

<template>
  <div id="app">
    <input type="text" v-model="id" placeholder="Student Id" />
    <input type="text" v-model="name" placeholder="Name" />
    <input type="text" v-model="email" placeholder="Email" />
    <input type="text" v-model="result" placeholder="Result" />
   
    <div class="button-container">
      <button v-on:click="postExaminationsResult">Key In Examination Result</button>
    </div>

    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>Student Name</th>
            <th>Email Address</th>
            <th>Result</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="exam in exams" :key="exam.StudentId">
            <td>{{ exam.Name }}</td>
            <td>{{ exam.EmailAddress }}</td>
            <td>{{ exam.Result }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
.table-container {
  margin-top: 2rem;
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.9rem;
}

th, td {
  padding: 0.75rem;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f2f2f2;
}

tr:hover {
  background-color: #f5f5f5;
}
</style>
