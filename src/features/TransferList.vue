<template>
  <b-container class="mt-5">
    <div class="d-flex justify-content-between align-items-center mb-3">
      <h1>Extrato de Transferências - Histórico</h1>
      <b-button variant="success" @click="$emit('openModal')" class="btn-lg">
        <font-awesome-icon icon="plus" class="mr-2"></font-awesome-icon>
        Nova transferência
      </b-button>
    </div>
    <div v-if="transfers.length === 0" class="text-center mt-5">
      <p>Você ainda não possui novas transferências. Clique no botão "Nova Transferência".</p>
    </div>
    <b-table v-else striped hover :items="transfers" :fields="fields" caption-top>
      <template #cell(originAccount)="row">
        {{ row.item.originAccount }}
      </template>
      <template #cell(destinationAccount)="row">
        {{ row.item.destinationAccount }}
      </template>
      <template #cell(amount)="row">
        R$ {{ row.item.amount }}
      </template>
      <template #cell(schedulingDate)="row">
        {{ formatDate(row.item.schedulingDate) }}
      </template>
      <template #cell(transferDate)="row">
        {{ formatDate(row.item.transferDate) }}
      </template>
    </b-table>
  </b-container>
</template>

<script>
import axios from 'axios';
import { format } from 'date-fns';
import { ptBR } from 'date-fns/locale';
import { EventBus } from '@/event-bus';

export default {
  name: 'TransferList',
  data() {
    return {
      transfers: [],
      fields: [
        { key: 'originAccount', label: 'Conta Origin' },
        { key: 'destinationAccount', label: 'Conta Destino' },
        { key: 'amount', label: 'Valor da Transferência' },
        { key: 'schedulingDate', label: 'Data de Agendamento' },
        { key: 'transferDate', label: 'Data da Transferência' },
      ],
    };
  },
  mounted() {
    this.fetchTransfers();
    EventBus.$on('transfer-created', this.fetchTransfers);
    
  },
  beforeDestroy() {
    EventBus.$off('transfer-created', this.fetchTransfers);
  },
  methods: {
    fetchTransfers() {
      axios.get('http://localhost:8080/api/transfers')
        .then(response => {
          this.transfers = response.data;
        })
        .catch(error => {
          console.error('Não foi possível achar transferências', error);
        });
    },
    formatDate(date) {
      if (!date) return '';
      return format(new Date(date), 'dd/MM/yyyy', { locale: ptBR });
    }
  }
};
</script>

<style scoped>
.text-center {
  text-align: center;
}
.mt-5 {
  margin-top: 3rem;
}
</style>
