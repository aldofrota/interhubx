<template>
  <div class="main--telacolaborador">
    <div class="lista--ordens"> 
      <div class="ordem" v-for="ordem in filtroOrdens" :key="ordem.id">

        <div class="titulo--ordem"><span>{{ ordem.titulo }}</span></div>

        <div class="colaborador--ordem"><span>Colaborador: </span><i>{{ ordem.colaborador }}</i></div>

        <div class="cliente--ordem"><span>Cliente:</span><i>{{ ordem.cliente }}</i></div>

        <div class="status--ordem"><span>Status: </span><i>{{ ordem.status }}</i></div>


        <div class="footer--ordem">
          <div class="data--ordem"><span>Aberta em </span>{{ ordem.data }}</div>

          <!-- Modal de descrição os elementos -->
          <b-button class="btn--ordem" @click="$bvModal.show('modal' + ordem.id)"> + detalhes </b-button>

          <b-modal :id="'modal' + ordem.id" hide-footer size="sm" title="Descrição" no-close-on-backdrop>
              <div class="modal--descricao">
                <div>{{ ordem.descricao }}</div>

                <div class="latlon">
                  <div class="latlon--ordem"><span>Latitude:</span><i>{{ ordem.latitude }}</i></div>
                  <div class="latlon--ordem"><span>Longitude: </span><i>{{ ordem.longitude }}</i></div>
                </div>
                
                <div class="footer--modal">
                  <b-button @click="finalizarOrdem(ordem.id)" v-if="ordem.status === 'Aberta'" >Finalizar Ordem</b-button>
                </div>
              </div>
          </b-modal>
        </div>
      </div>
    </div>

    <!-- Div onde fica o botão flutuante -->
    <div
      v-b-popover.hover.top="'Adicionar nova ordem de serviço'"
      class="flutuante"
      @click="abrirModal">
      <span class="material-icons"> add_box </span>
    </div>

    <!-- Modal de adicionar nova ordem -->
    <div>
      <b-modal v-model="modalAdicionar" hide-header hide-footer size="sm" no-close-on-backdrop>
        <div class="conteudo--modal">
          <div class="titulo--modal">
            <span>Cadastrar nova ordem</span>
            <span class="btn--fechar material-icons" @click="modalAdicionar = !modalAdicionar; limparForm()">close</span>
          </div>
          <b-form @submit="novaOrdem">
            <div class="input-container">
              <label for="nome">Titulo:</label>
              <input type="text" v-model="ordem.titulo" id="titulo" placeholder="Digite o titulo da ordem de serviço" required >
            </div>
            <div class="input-container">
              <label for="descricao">Descrição:</label>
              <textarea v-model="ordem.descricao" id="descricao" placeholder="Digite a descrição da ordem de serviço" required wrap="hard"/>
            </div>
            <div class="input-container">
              <label for="colaborador">Colaborador:</label>
              <select v-model="ordem.colaborador" id="colaborador" required>
                <option value="" disabled selected>--Selecione--</option>
                <option v-for="colaborador in colaboradores" :key="colaborador.id" :value="colaborador.nome">{{colaborador.nome}}</option>
              </select>
            </div>
            <div class="input-container">
              <label for="cliente">Cliente:</label>
              <select v-model="ordem.cliente" id="perfil" required>
                <option value="" disabled selected>--Selecione--</option>
                <option v-for="cliente in clientes" :key="cliente.id" :value="cliente.nome">{{cliente.nome}}</option>
              </select>
            </div>
            
            <div class="btn--salvar">
              <b-button type="submit" ><b-icon class="icone" icon="file-earmark-arrow-up-fill"/>Salvar</b-button>
            </div>
          </b-form>
        </div>
      </b-modal>
    </div>
  </div>
</template>

<script>
export default {
  
  data() {
    return {
      ordem: {
        titulo: '',
        descricao: '',
        colaborador: '',
        cliente: '',
        status: '',
        latitude: '',
        longitude: '',
        data: ''
      },
      modalAdicionar: false,
      colaboradores: [],
      clientes: [],
      filtroOrdens: [],
      ordens: [
        {
          id: 1,
          titulo: 'Ordem de serviço 1',
          colaborador: 'João',
          cliente: 'Cliente 1',
          status: 'Aberta',
          latitude: '-23.564',
          longitude: '-46.654',
          data: '01/01/2019',
          descricao: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod, nisl eget consectetur tempor, nisi nisl euismod nunc, eget consectetur nisl nisl euismod nunc. Donec euismod,'
        }
        
      ]
    }
  }

}
</script>