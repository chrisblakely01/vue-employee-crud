<template>
  <div id="app" class="small-container">
    <h1>Employees</h1>

    <!-- This component is imported like ES6 modules; names must match -->
    <!-- Use the : to pass data as props -->
    <!-- Use the @ to listen for events -->
    <p v-if="loading === true">Loading...</p>
    <employee-table
      :employees="employees"
      @delete:employee="deleteEmployee"
      @edit:employee="editEmployee"
    />
    <!-- This listens for an event and calls a method(function) -->
    <employee-form @add:employee="addEmployee" />
  </div>
</template>

<script>
import EmployeeTable from "@/components/EmployeeTable.vue";
import EmployeeForm from "@/components/EmployeeForm.vue";

export default {
  name: "app",
  // Registers a components for use within this one
  components: {
    EmployeeTable,
    EmployeeForm
  },
  // Data is like React State
  data() {
    return {
      loading: false,
      employees: []
    };
  },
  // Equivalent of component did mount
  mounted() {
    this.getEmployees();
  },
  methods: {
    async getEmployees() {
      try {
        this.loading = true;
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users"
        );
        const data = await response.json();
        this.employees = data;
      } catch (error) {
        console.error(error);
      } finally {
        this.loading = false;
      }
    },
    // This gets called when an add:employee event is "caught"
    // Payload gets wired in
    async addEmployee(employee) {
      try {
        this.loading = true;
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users",
          {
            method: "POST",
            body: JSON.stringify(employee),
            headers: { "Content-type": "application/json; charset=UTF-8" }
          }
        );
        const data = await response.json();
        this.employees = [...this.employees, data];
      } catch (error) {
        console.error(error);
      } finally {
        this.loading = false;
      }
    },
    async deleteEmployee(id) {
      try {
        this.loading = true;
        await fetch(`https://jsonplaceholder.typicode.com/users/${id}`, {
          method: "DELETE"
        });
        this.employees = this.employees.filter(employee => employee.id !== id);
      } catch (error) {
        console.error(error);
      } finally {
        this.loading = false;
      }
    },
    async editEmployee(id, updatedEmployee) {
      try {
        this.loading = true;
        const response = await fetch(
          `https://jsonplaceholder.typicode.com/users/${id}`,
          {
            method: "PUT",
            body: JSON.stringify(updatedEmployee),
            headers: { "Content-type": "application/json; charset=UTF-8" }
          }
        );
        const data = await response.json();
        this.employees = this.employees.map(employee =>
          employee.id === id ? data : employee
        );
      } catch (error) {
        console.error(error);
      } finally {
        this.loading = false;
      }
    }
  }
};
</script>

<style>
button {
  background: #009435;
  border: 1px solid #009435;
}

.small-container {
  max-width: 680px;
}
</style>