<template>
  <div>
    <h1 class="alert alert-dark" role="alert">Email Migration</h1>
    <div v-show="ShowTable()" class="container">
      <table class="table table-hover table-dark">
          <thead class="thead-inverse">
              <tr>
              <th>Name</th>
              <th>Email Source</th>
              <th>Email Destination</th>
              <th>Update? <input type="checkbox" v-on:click="CheckAll()" id="checkbox" v-model="allChecked"></th>
              </tr>
          </thead>
          <tbody>
            <tr v-for="email in emails">
              <td>{{email.name}}</td>
              <td>{{email.emailSource}}</td>
              <td>{{email.emailDestination}}</td>
              <td><input type="checkbox" v-model="email.checked"></td>
            </tr>
          </tbody>
      </table>
    </div>
    <button v-show="ShowUpdate()" type="button" v-on:click="UpdateEmails()" class="btn btn-dark">Update</button>
  </div>
</template>

<script>
export default {
  name: "Table",
  data() {
    return {
      emails: [],
      allChecked: false
    };
  },
  methods: {
    UpdateEmails() {
      fetch("http://localhost:54632/api/email", {
        headers: {
          "Content-Type": "application/json"
        },
        method: "post",
        body: JSON.stringify(
          this.emails.filter(email => email.checked === true)
        )
      })
        .then(response => response.json())
        .then(response => {
          this.refresh();
        })
        .catch(error => {
          console.log("error: ", error);
        });
    },
    refresh() {
      this.emails = this.emails.filter(email => email.checked === false);
    },
    ShowUpdate(){
      return this.emails.filter(email => email.checked === true).length > 0;
    },
    ShowTable(){
      return this.emails.length > 0;
    },
    CheckAll(){
      this.emails.forEach(element => {
        element.checked = !this.allChecked
      });
    }
  },
  created() {
    fetch("http://localhost:54632/api/email")
      .then(response => response.json())
      .then(data => {
        this.emails = data;
      });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
