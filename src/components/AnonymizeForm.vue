<template>
  <div class="nav flex-column nav-pills p-3" id="v-pills-tab" role="tablist" aria-orientation="vertical">
    <div class="col mx-auto text-center p-3">
    <img src="@/assets/logo.png" height="50" width="50">
    </div>
    <div class="form-group">
      <input v-model="username" type="text" class="form-control" placeholder="Username">
    </div>
    <div class="form-group">
      <input v-model="password" type="password" class="form-control" placeholder="Password">
    </div>
    <button @click="login" class="btn btn-primary mt-2 bg-dark">Login</button>
    <button @click="register" class="btn btn-primary mt-2 bg-dark">Register</button>
  </div>
  <div class="container mt-4">
    <div class="form-group">
      <label for="jsonInput">Payload</label>
      <textarea
        v-model="jsonInput"
        id="jsonInput"
        class="form-control"
        rows="6"
        placeholder="Ex.: { 'execution_parameters': [ { 'algorithm', 'configuration', 'columns' } ], 'data' : [ { Object_1 }... ] }"
      ></textarea>
    </div>
    <p align="right" class="p-3 mx-3"><button
      @click="postAnonymize"
      class="btn btn-primary m-2 bg-dark"
    >
      Anonymize
    </button>
    <button
      @click="postResult"
      class="btn btn-primary m-2 bg-dark"
    >
      Result
    </button></p>
    <div v-if="responseField" class="form-group mt-4">
      <label for="responseField">Response</label>
      <textarea
        ref="responseField"
        id="responseField"
        class="form-control response"
        :rows="calculateResponseRows"
        v-model="responseField"
        readonly
      ></textarea>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      jsonInput: "",
      responseField: "",
      sessionToken: "", // Armazenar o token de sessão aqui
    };
  },
  methods: {
    async postAnonymize() {
      try {
        const jsonData = JSON.parse(this.jsonInput);

        const response = await fetch("http://127.0.0.1:8000/anonymize", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Token ${this.sessionToken}`, // Adiciona o token de sessão no cabeçalho da requisição
          },
          body: JSON.stringify(jsonData),
        });

        const data = await response.json();
        this.responseField = JSON.stringify(data, null, 2);
      } catch (error) {
        console.error(error);
      }
    },
    async postResult() {
      try {
        const response = await fetch("http://127.0.0.1:8000/result", {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Token ${this.sessionToken}`, // Adiciona o token de sessão no cabeçalho da requisição
          }
        });

        const data = await response.json();
        this.responseField = JSON.stringify(data, null, 2);
      } catch (error) {
        console.error(error);
      }
    },
    async login() {
      try {
        const jsonData = {
          username: this.username, // Usando o valor do input do nome de usuário
          password: this.password, // Usando o valor do input da senha
        };

        const response = await fetch("http://127.0.0.1:8000/login", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(jsonData),
        });

        const data = await response.json();
        this.sessionToken = data.token;
      } catch (error) {
        console.error(error);
      }
    },
    async register() {
      try {
        const jsonData = {
          username: this.username, // Usando o valor do input do nome de usuário
          password: this.password, // Usando o valor do input da senha
        };

        const response = await fetch("http://127.0.0.1:8000/register", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(jsonData),
        });

        const data = await response.json();
        this.sessionToken = data.token;
      } catch (error) {
        console.error(error);
      }
    },
  },
  computed: {
    calculateResponseRows() {
      return this.responseField.split(/\r\n|\r|\n/).length + 1;
    },
  },
};
</script>

<style>
.response {
  white-space: pre-wrap;
  background-color: #f8f9fa;
  padding: 10px;
  height: auto;
}
</style>
