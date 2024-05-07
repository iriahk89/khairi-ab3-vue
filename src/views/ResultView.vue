<script setup>
  import { generateClient } from 'aws-amplify/api';
  import { onMounted, ref } from 'vue';
  import { get } from 'aws-amplify/api';

  const exams = ref([]);

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
