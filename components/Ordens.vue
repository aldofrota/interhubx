<template>
  <div class="main--ordens">
    <div class="filtros">
      <select v-model="filtro.colaborador" id="colaborador">
        <option value="" selected>Colaboradores</option>
        <option v-for="colaborador in colaboradores" :key="colaborador.id" :value="colaborador.nome">{{ colaborador.nome }}</option>
      </select>
      <select v-model="filtro.cliente" id="cliente">
        <option value="" selected>Clientes</option>
        <option v-for="cliente in clientes" :key="cliente.id" :value="cliente.nome">{{ cliente.nome }}</option>
      </select>
      <select v-model="filtro.status" id="status">
        <option value="" selected>Status</option>
        <option value="Aberto" selected>Aberto</option>
        <option value="Finalizado" selected>Finalizado</option>
      </select>
    </div>
    {{ filtro.colaborador }}
    {{ filtro.cliente }}
    {{ filtro.status }}
    <div class="lista--ordens"> 
      <div class="ordem" v-for="ordem in filtroOrdens" :key="ordem.id">

        <div class="titulo--ordem"><span>{{ ordem.titulo }}</span></div>

        <div class="colaborador--ordem"><span>Colaborador: </span><i>{{ ordem.colaborador }}</i></div>

        <div class="cliente--ordem"><span>Cliente:</span><i>{{ ordem.cliente }}</i></div>

        <div class="status--ordem"><span>Status: </span><i>{{ ordem.status }}</i></div>


        <div class="footer--ordem">
          <div class="data--ordem">{{ ordem.data }}</div>

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
    <div
      v-b-popover.hover.top="'Adicionar nova ordem de serviço'"
      class="flutuante"
      @click="modalAdicionar = !modalAdicionar">
      <span class="material-icons"> add_box </span>
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
              <label class="label" for="colaborador">Colaborador:</label>
              <select v-model="ordem.colaborador" id="colaborador">
                <option :value="null" selected disabled required>--Selecione--</option>
                <option v-for="colaborador in colaboradores" :key="colaborador.id" :value="colaborador.nome">{{colaborador.nome}}</option>
              </select>
            </div>
            <div class="input-container">
              <label class="label" for="cliente">Cliente:</label>
              <select id="perfil"></select>
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

      filtro: {
        colaborador: '',
        cliente: '',
        status: ''
      },

      ordem: {
        titulo: '',
        descricao: '',
        colaborador: '',
        cliente: '',
      },

      ordens:[],
      colaboradores:[],
      clientes:[],

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
    
  },

  computed: {

    filtroOrdens() {

      let valores = []

      // Filtro de Ordens de serviço pelo colaborador
      valores = this.ordens.filter((item) => {
        if (this.filtro.colaborador === null || this.filtro.colaborador.length === 0) { return item }

        return item.colaborador.toLowerCase() === this.filtro.colaborador.toLowerCase()
      })

      // Filtro de ordens de serviço pelo cliente
      valores = this.ordens.filter((item) => {
        if (this.filtro.cliente === null || this.filtro.cliente.length === 0) { return item }
        
        return item.cliente.toLowerCase() === this.filtro.cliente.toLowerCase()
      })

      // Filtro de ordens de serviço pelo status
      valores = this.ordens.filter((item) => {
        if (this.filtro.status === null || this.filtro.status.length === 0) { return item }
        
        console.log(item.status)
        return item.status.toLowerCase() === this.filtro.status.toLowerCase()
      })

      return valores;
    }
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
    align-items: flex-start;
    justify-content: space-between;
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

  .flutuante {
    position: fixed;
    bottom: 20px;
    right: 20px;
    cursor: pointer;
    height: 40px;
    width: 40px;
  }
  .flutuante span {
    font-size: 50px;
    transition: all 0.5s;
    background-color: #f5f5f5;
    height: 40px;
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
  }
  .flutuante span:hover {
    transform: scale(1.2);
  }


  @media only screen and (max-width: 280px) {
    .lista--ordens .ordem {
      width: 95%;
    }
    .titulo--ordem {
      font-size: 17px;
    }
    .colaborador--ordem {
      font-size: 14px;
    }
    .cliente--ordem {
      font-size: 14px;
    }
    .status--ordem {
      font-size: 14px;
    }

    .flutuante span {
      height: 30px;
      width: 30px;
    }	
  }

  @media only screen and (max-width: 360px) {
    .filtros select {
      width: 25%;
      margin: 0;
    }
    .flutuante span {
      height: 20px;
      width: 20px;
      font-size: 30px;
    }
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
    .flutuante span {
      height: 30px;
      width: 30px;
      font-size: 40px;
    }
  }
</style>