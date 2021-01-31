<template>
  <div class="main-component">
    <h1>TimeMan</h1>
    <hr>
    <div class="row">
      <div class="col-12">
        <h4>Create new event</h4>
        <form @submit.prevent="submitForm">
          <div class="form-group row">
            <div class="col-3">
              <input type="text" class="form form-control col-3 mx-2" placeholder="Event" v-model="table.event">
            </div>
            <div class="col-3">
              <input type="date" class="form form-control col-3 mx-2" placeholder="Start" v-model="table.start">
            </div>
            <div class="col-3">
              <input type="date" class="form form-control col-3 mx-2" placeholder="End" v-model="table.end">
            </div>
            <div class="col-3">
              <button class="btn btn-success">Submit</button>
            </div>
          </div>

        </form>
        <hr>

        <table class="table">
          <thead>
            <th>Event</th>
            <th>Start</th>
            <th>End</th>
          </thead>
          <tbody>
            <tr v-for="table in tables" :key="table.id">
              <td>{{ table.event }}</td>
              <td>{{ table.start }}</td>
              <td>{{ table.end }}</td>
              <td>
                <button class="brn btn-success btn-sm mx-1" @click="$data.table = table">Edit</button>
                <button class="brn btn-danger btn-sm mx-1" @click="deleteTable(table)">Delete</button>
                <button class="brn btn-danger btn-sm mx-1" @click="deleteTableAxios(table)">Delete Axios</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style lang="stylus" scoped>

</style>

<script>
  import axios from 'axios';

  export default {
    name: 'Components',
    data() {
      return {
        table: {},
        tables: [],
        myDate : new Date().toISOString().slice(0,10)
      }
    },
    async created() {
      await this.getTablesAxios();
    },

    methods: {
      submitForm() {
        if (this.table.id === undefined) {
          this.createTableAxios();
        } else {
          this.editTableAxios();
        }
      },


      async getTables() {
        var response = await fetch('http://localhost:8000/database/tables/');
        this.tables = await response.json();
      },
      async createTable() {
        await this.getTables();

        await fetch('http://localhost:8000/database/tables/', {
          method: 'post',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.table)
        });
        await this.getTables();
      },
      async editTable() {
        await this.getTables();

        await fetch(`http://localhost:8000/database/tables/${this.table.id}/`, {
          method: 'put',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.table)
        });
        await this.getTables();
        this.table = {};
      },
      async deleteTable(table) {
        await this.getTables();

        await fetch(`http://localhost:8000/database/tables/${table.id}/`, {
          method: 'delete',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.table)
        });
        await this.getTables();
        this.table = {};
      },


      async getTablesAxios() {
        var response = await axios({
          url: 'http://localhost:8000/database/tables/',
          method: 'get',
        });
        this.tables = await response.data;
      },

      
      async createTableAxios() {
        await this.getTables();

        await axios({
          url: 'http://localhost:8000/database/tables/',
          method: 'post',
          headers: {
            'Content-Type': 'application/json'
          },
          data: JSON.stringify(this.table)
        });
        await this.getTables();
      },
      async editTableAxios() {
        await this.getTables();

        await fetch({
          url: `http://localhost:8000/database/tables/${this.table.id}/`,
          method: 'put',
          headers: {
            'Content-Type': 'application/json'
          },
          data: JSON.stringify(this.table)
        });
        await this.getTables();
        this.table = {};
      },

      async deleteTableAxios(table) {
        await this.getTables();

/*
        await axios.detele(`http://localhost:8000/database/tables/${table.id}/`, 
          { headers: { 'Content-Type': 'application/json' } },
          { data: { source: JSON.stringify(this.table) }});
*/

        //await axios.delete(`http://localhost:8000/database/tables/${table.id}/`, { data: JSON.stringify(this.table) });
        
        await axios({
          url: `http://localhost:8000/database/tables/${table.id}/`,
          method: 'delete',
          headers: {
            'Content-Type': 'application/json'
          },
          data: JSON.stringify(this.table)
        });
        
        await this.getTables();
        this.table = {};
      }
    }

  }
</script>