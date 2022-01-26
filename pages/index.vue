<template>
  <div>
    <h1>Index Page</h1>
    <h2>{{ usuario.nome }}</h2>
    <h3>{{ usuario.perfil }}</h3>
    <button @click="logout()" >Sair</button>
  </div>
</template>

<script>
export default {

  data() {
    return {
      usuario: {
        nome: '',
        perfil: ''
      },
    }
  },

  mounted() {
    if (!localStorage.nome) {
      this.$router.push('/login')
    }
    if (localStorage.nome) {
      this.usuario.nome = localStorage.nome
    }
    if (localStorage.perfil) {
      this.usuario.perfil = localStorage.perfil
    }
  },
  methods: {
    logout() {
      localStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>
