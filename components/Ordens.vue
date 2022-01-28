<template>
  <div class="main--ordens">
    <div class="filtros">
      <select  id="colaborador">
        <option value="" selected>Colaboradores</option>
        <option v-for="colaborador in colaboradores" :key="colaborador.id" :value="colaborador.nome">{{ colaborador.nome }}</option>
      </select>
      <select id="cliente">
        <option value="" selected>Clientes</option>
        <option v-for="cliente in clientes" :key="cliente.id" :value="cliente.nome">{{ cliente.nome }}</option>
      </select>
      <select id="status">
        <option value="" selected>Status</option>
        <option value="Aberto" selected>Aberto</option>
        <option value="Finalizado" selected>Finalizado</option>
      </select>
    </div>
    <div class="lista--ordens"> 
      <div class="ordem" v-for="ordem in ordens" :key="ordem.id">

        <div class="titulo--ordem"><span>{{ ordem.titulo }}</span></div>

        <div class="colaborador--ordem"><span>Responsavel: </span><i>{{ ordem.colaborador }}</i></div>

        <div class="cliente--ordem"><span>Cliente:</span><i>{{ ordem.cliente }}</i></div>

        <div class="status--ordem"><span>Status: </span><i>{{ ordem.status }}</i></div>


        <div class="footer--ordem">
          <div class="data--ordem">{{ ordem.createdAt.split('-') }}</div>

          <b-button class="btn--ordem" @click="modalDetalhes = !modalDetalhes"> + detalhes </b-button>
          <b-modal v-model="modalDetalhes" hide-header hide-footer size="sm">
            <span class="descricao--ordem">Descrição</span>

            <div>{{ ordem.descricao }}</div>
            
            <div class="footer--modal">
              <b-button @click="modalDetalhes = !modalDetalhes"> Fechar </b-button>
              <span @click="finalizar">Finalizar</span>
            </div>
          </b-modal>
        </div>
      </div>
    </div>

    <div>
      <b-modal v-model="modalAdicionar" hide-header hide-footer size="sm">
        <div class="conteudo--modal">
          <span>Cadastrar nova ordem:</span>
          <form @submit.prevent="novocolaborador">
            <div class="input-container">
              <label class="label" for="nome">Titulo:</label>
              <input class="input" type="text" v-model="ordem.titulo" id="titulo" placeholder="Digite o titulo da ordem de serviço" required >
            </div>
            <div class="input-container">
              <label class="label" for="descricao">Descrição:</label>
              <input class="input" type="textarea" v-model="ordem.descricao" id="descricao" placeholder="Digite a descrição da ordem de serviço" required >
            </div>
            <div class="input-container">
              <label class="label" for="perfil">Perfil:</label>
              <b-form-select v-model="selected" :options="options" required></b-form-select>
            </div>
            <div class="input-container">
              <label class="label" for="perfil">Perfil:</label>
              <b-form-select v-model="selected" :options="options" required></b-form-select>
            </div>
            
            <div class="btn--salvar">
              <button><b-icon class="icone" icon="file-earmark-arrow-up-fill"/>Salvar</button>
            </div>
          </form>
        </div>
      </b-modal>
    </div>
  </div>
</template>

<script>


export default {

  

  data() {
    return {

      modalDetalhes: false,
      modalAdicionar: false,

      ordem: {
        colaborador: '',
        cliente: '',
        status: ''
      },

      ordens:[],
      colaboradores:[],
      clientes:[],

      bgColor: '#252525',
      position: 'botton-right',
      fabActions: [
          {
              name: 'atualizar',
              icon: 'cached'
          },
          {
              name: 'adicionar',
              icon: 'note_add'
          }
      ]
    }
  },

  methods: {

    finalizar() {
      this.modalShow = !this.modalShow;
    },

    async listarOrdens() {
      const response = await this.$axios.get('ordens').then(response => {
        this.ordens = response.data
      }).catch(erro => {
        console.log(erro)
      });
    },
    async listarColaboradores() {
      const response = await this.$axios.get('colaboradores').then(response => {
        this.colaboradores = response.data
      }).catch(erro => {
        console.log(erro)
      });
    },
    async listarClientes() {
      const response = await this.$axios.get('clientes').then(response => {
        this.clientes = response.data
      }).catch(erro => {
        console.log(erro)
      });
    },

    async novaOrdem() {
      if(this.ordem.titulo != '') {
        await this.$axios.post('registroordem', {

          titulo: this.ordem.titulo,
          descricao: this.ordem.descricao,
          colaborador: this.ordem.colaborador,
          cliente: this.ordem.cliente

        })

        this.listarOrdens()
        this.ordem.titulo = ''
        this.ordem.descricao = ''
        this.ordem.colaborador = ''
        this.ordem.cliente = ''

        this.$bvToast.toast('Ordem cadastrada com sucesso', {
          title: 'Usuário cadastrado',
          variant: 'success',
          autoHideDelay: 2000,
          solid: true
        })

        this.modalAdicionar = !this.modalAdicionar;
      
      } else {
        return
      }
    },    
  },
  
  mounted() {

    this.listarOrdens()
    this.listarColaboradores()
    this.listarClientes()
    
  }
}
</script>


<style>

  .main--ordens {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin-top: 20px;
    padding-top: 20px;
  }

  .filtros {
    display: flex;
  }
  .filtros select {
    width: 200px;
    margin-right: 20px;
    padding: 2px;
    border-radius: 5px;
    border: 1px solid #b3aeae;
  }


  .lista--ordens{
    margin-top: 20px;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }
  .lista--ordens .ordem {
    height: 300px;
    width: 300px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: flex-start;
    padding: 10px;
    margin: 10px 20px 40px 10px;
    border-radius: 5px;
    background: linear-gradient(225deg, #ffffff, #dddddd);
    box-shadow:  -20px 20px 60px #d0d0d0,
             20px -20px 60px #ffffff;
  }

  .titulo--ordem {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    font-weight: bold;
    color: #252525;
  }

  .colaborador--ordem {
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }
  .colaborador--ordem span {
    color: #252525;
    font-weight: bold;
    margin-right: 10px;
  }

  .cliente--ordem {
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }
  .cliente--ordem span {
    color: #252525;
    font-weight: bold;
    margin-right: 10px;
  }

  .status--ordem {
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }
  .status--ordem span {
    color: #252525;
    font-weight: bold;
    margin-right: 10px;
  }
  .footer--ordem {
    display: flex;
    width: 100%;
    justify-content: space-between;
    align-items: center;
    height: 50px;
  }
  .footer--ordem .data--ordem {
    font-size: 12px;
  }
  .footer--ordem .btn--ordem {
    height: 30px;
    width: 70px;
    font-size: 12px;
    padding: 0;
    margin-right: 5px;
  }

  @media only screen and (max-width: 700px) {

    .lista--ordens .ordem {
      height: 180px;
      border-radius: 5px;
      background: linear-gradient(225deg, #ffffff, #dddddd);
      box-shadow:  -20px 20px 20px #d0d0d0,
                    20px -20px 20px #ffffff;
    }
    .filtros {
      display: flex;
      justify-content: space-around;
      align-items: center;
    }
    .filtros select {
      width: 30%;
      margin: 0;
    }
  }
  @media only screen and (max-width: 360px) {
    .filtros select {
      width: 25%;
      margin: 0;
    }
  }
</style>