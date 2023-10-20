<template>
  <div>

    <div class="container">
      <h1 class="my-4">Wayon Transfer Form</h1>
      <div class="row">

        <div class="col-md-6">
          <div class="form-group">
            <label for="contaOrigem">Conta Origem:</label>
            <input v-model="transfer.contaOrigem" type="text" id="contaOrigem" class="form-control" required>
          </div>

          <div class="form-group">
            <label for="valor">Valor:</label>
            <input v-model="transfer.valor" type="number" id="valor" class="form-control" required>
          </div>

          <div class="form-group">
            <label for="dataAgendamento">Data Agendamento:</label>
            <input v-model="transfer.dataAgendamento" type="text" id="dataAgendamento" class="form-control"  readonly>
          </div>
        </div>

        <div class="col-md-6">
          <div class="form-group">
            <div class="form-group">
              <label for="contaDestino">Conta Destino:</label>
              <input v-model="transfer.contaDestino" type="text" id="contaDestino" class="form-control" required>
            </div>
          </div>
          <div class="form-group">
            <label for="dataTransferencia">Data Transferencia:</label>
            <input v-model="transfer.dataTransferencia" type="date" id="dataTransferencia" :min="getCurrentDate()" class="form-control" required>
          </div>
        </div>
      </div>

      <button @click.prevent="submitForm" class="btn btn-primary mt-3">Submit</button>
    </div>

    <div class="container text-center mt-5">
      <h1 class="my-4">Wayon Bank Statment Search</h1>

      <div class="row justify-content-center">
        <div class="col-md-6">
          <div class="input-group">
            <input v-model="searchAccount" type="text" class="form-control" placeholder="Digite o número da conta">
            <div class="input-group-append">
              <button @click.prevent="searchByAccount" class="btn btn-outline-secondary" type="button">Pesquisar</button>
            </div>
          </div>
        </div>
      </div>

      <div class="row justify-content-center mt-4">
        <div class="col-md-6">

          <h3>Resultados da Pesquisa</h3>
          <table class="table table-bordered">
        <thead>
          <tr>
            <th>Conta Origem</th>
            <th>Conta Destino</th>
            <th>Valor</th>
            <th>Taxa</th>
            <th>Data de Agendamento</th>
            <th>Data de Transferência</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(result, index) in searchResults" :key="index">
            <td>{{ result.contaOrigem }}</td>
            <td>{{ result.contaDestino }}</td>
            <td>{{ result.valor }}</td>
            <td>{{ result.taxa }}</td>
            <td>{{ result.dataAgendamento }}</td>
            <td>{{ result.dataTransferencia }}</td>
          </tr>
        </tbody>
      </table>
          <div v-if="searchResults.length === 0">
            <p>Nenhum resultado encontrado. Verifique conta digitada</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {

    const currentDate = new Date();
    const initialDate = currentDate.toLocaleDateString('en-CA');
    return {
      transfer: {
        contaOrigem: '',
        contaDestino: '',
        valor: null,
        taxa: null,
        dataTransferencia: '',
        dataAgendamento: initialDate
      },
      searchAccount: '',
      searchResults: []  
    };
  },
  methods: {

    async searchByAccount() {
    try {
      var myHeaders = new Headers();

        var myInit = {
          method: "GET",
          headers: myHeaders,
          mode: "cors",
          cache: "default",
        };
      const apiUrl = `http://localhost:8080/wayon?conta=${this.searchAccount}`;
      const response = await fetch(apiUrl,myInit); 

      if (response.ok) {
        const data = await response.json();
        this.searchResults = data;
      } else {
        console.error('Error:', response.statusText);
        this.searchResults = [];
      }
    } catch (error) {
      console.error('Error:', error);
      this.searchResults = [];
    }
  },
  submitForm() {
      const apiUrl = 'http://localhost:8080/wayon';
      const requestData = JSON.stringify(this.transfer);
      
      fetch(apiUrl, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: requestData
      })
      .then(response => response.json())
      .then(data => {
        console.log('Response from server:', data);
      })
      .catch(error => {
        console.error('Error:', error);
      });

  },
    getCurrentDate() {
        const today = new Date();
        const year = today.getFullYear();
        let month = today.getMonth() + 1;
        let day = today.getDate();

        if (month < 10) {
          month = `0${month}`;
        }

        if (day < 10) {
          day = `0${day}`;
        }

        return `${year}-${month}-${day}`;
      }
  }
};
</script>