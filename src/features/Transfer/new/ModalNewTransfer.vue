<template>
  <b-modal :visible="isModalVisible" @hide="closeModal" hide-footer>
    <template #modal-title>
      <div class="d-flex justify-content-between w-100">
        <h5 class="modal-title">Nova Transferência</h5>
        <b-button @click="closeModal" variant="link" aria-label="Close">
          <font-awesome-icon icon="times" />
        </b-button>
      </div>
    </template>
    <form @submit.prevent="createTransfer">
      <b-form-group label="Conta de Origem" class="mb-4">
        <b-form-input v-model="originAccount" type="text" required></b-form-input>
      </b-form-group>
      <b-form-group label="Conta Destino" class="mb-4">
        <b-form-input v-model="destinationAccount" type="text" required></b-form-input>
      </b-form-group>
      <b-form-group label="Valor da Transferência" class="mb-4">
        <b-form-input v-model="amount" type="number" required></b-form-input>
      </b-form-group>
      <b-form-group class="mb-4">
        <b-form-checkbox v-model="scheduledTransfer">Deseja agendar a transferência?</b-form-checkbox>
      </b-form-group>
      <b-form-group v-if="scheduledTransfer" class="mb-4">
        <b-form-datepicker v-model="scheduledDate" required></b-form-datepicker>
      </b-form-group>
      <div class="d-flex justify-content-end">
        <b-button type="submit" variant="success">Concluir</b-button>
      </div>
    </form>
  </b-modal>
</template>

<script>
import axios from 'axios';
import { EventBus } from '@/event-bus';

export default {
  name: 'ModalNewTransfer',
  props: ['showModal'],
  data() {
    return {
      isModalVisible: this.showModal,
      originAccount: '',
      destinationAccount: '',
      amount: '',
      scheduledTransfer: false,
      scheduledDate: null,
    };
  },
  watch: {
    showModal(newVal) {
      this.isModalVisible = newVal;
      if (newVal) {
        this.resetForm();
      }
    },
    isModalVisible(newVal) {
      if (!newVal) {
        this.$emit('closeModal');
      }
    }
  },
  methods: {
    resetForm() {
      this.originAccount = '';
      this.destinationAccount = '';
      this.amount = '';
      this.scheduledTransfer = false;
      this.scheduledDate = null;
    },
    createTransfer() {
      const currentDate = new Date().toISOString();
      let transferDate = currentDate;
      let schedulingDate = currentDate;

      if (this.scheduledTransfer && this.scheduledDate) {
        transferDate = new Date(this.scheduledDate).toISOString();
        schedulingDate = transferDate;
      }

      const newTransfer = {
        originAccount: this.originAccount,
        destinationAccount: this.destinationAccount,
        amount: this.amount,
        transferDate: transferDate,
        schedulingDate: schedulingDate,
      };

      axios.post('http://localhost:8080/api/transfers', newTransfer)
        .then(response => {
          this.isModalVisible = false;
          this.$root.$emit('transfer-updated');
          EventBus.$emit('transfer-created');
        })
        .catch(error => {
          console.error('Error creating transfer:', error);
        });
    },
    closeModal() {
      this.isModalVisible = false;
    }
  }
};
</script>


<style scoped>
.mb-4 {
  margin-bottom: 1.5rem;
}

</style>
