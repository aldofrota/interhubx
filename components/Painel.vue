<template>
  <div class="main--painel">
    <div class="card--painel" v-for="dado in dados" :key="dado.dia">
      <div class="card--conteudo">
        <i class="titulo--card"><span>Dia</span>{{ dado.dia }}</i>
        <i>Teve {{ dado.qtde }} <span v-if="dado.qtde === 1"> ordem aberta</span><span v-else >ordens abertas</span></i>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  
  data() {
    return {
     
      dados: []

    }
  },

  methods: {
    
    async listarDados() {
      const response = await this.$axios.get('dadospainel').then(response => {

        this.dados = response.data;
      }).catch(erro => {
        console.log(erro)
      });
    },

  },

  mounted() {
    this.listarDados();
  },

}

</script>

<style>
  
  .main--painel {
    width: 95%;
    height: 100%;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
  }
  .card--painel {
    width: 150px;
    height: 150px;
    background-color: #f5f5f5;
    color: #252525;
    border-radius: 50px;
    background: linear-gradient(145deg, #ffffff, #dddde6);
    box-shadow:  20px 20px 50px #d0d0d9,
                -20px -20px 60px #ffffff;
    margin: 10px;
  }
  .card--conteudo {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .card--conteudo i {
    font-size: 20px;
    margin-top: 10px;
    text-align: center;
  }
  .card--conteudo .titulo--card span {
    margin-right: 5px;
  }


</style>