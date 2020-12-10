<template>
  <div>
    <div>usuario autenticado: {{user.name}}</div>
	<button @click="logout" v-if="user.email">Logout</button>
    <form v-else @submit.prevent="login">
      <input v-model="form.email" type="email" placeholder="Email..." /> <br />
      <input
        v-model="form.password"
        type="password"
        placeholder="Password..."
      />
      <br />
      <button>Login</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";
axios.defaults.baseURL = "http://127.0.0.1:8000";
export default {
  data: () => ({
    user: {},
    form: {
      email: "juanhurtado@payco",
      password: "1234567",
    },
  }),
  created() {
    if (localStorage.getItem("token")) {
		let configuration = {
			method: "GET",
			url: "api/auth/profile",
			headers: {
				Authorization: `Bearer ${localStorage.getItem("token")}`,
			},
		};
		axios(configuration)
		.then((response) => {
			this.user = response.data.data
		})
		.catch((error) => {
			console.log(error.response.status);
			console.log(error.response.data);
		});
    }
    
  },
  methods: {
    login() {
      axios
        .post("/api/auth/login", this.form)
        .then((response) => {
		  localStorage.setItem("token", response.data.token);
		  this.user = response.data.user
        })
        .catch((error) => {
          console.log(error.response.status);
          console.log(error.response.data);
        });
	},
	logout(){
		let configuration = {
			method: "POST",
			url: "api/auth/logout",
			headers: {
				Authorization: `Bearer ${localStorage.getItem("token")}`,
			},
		};

		axios(configuration)
        .then((response) => {
			console.log(response);
		  	localStorage.removeItem("token");
		  	this.user = {}
        })
        .catch((error) => {
          console.log(error.response.status);
          console.log(error.response.data);
        });
	}
  },
};
</script>

<style lang="scss" scoped>
</style>