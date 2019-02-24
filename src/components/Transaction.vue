<template>
  <div class="q-pa-md q-gutter-sm">
    <q-btn fab color="positive" @click="isTransacting = true" >
      <q-icon name="attach_money" />
    </q-btn>
    <q-dialog v-model="isTransacting">
      <q-card>
        <q-card-section>
          <div class="text-h6">Send or Request</div>
        </q-card-section>

        <q-card-section>
          <q-input v-model="recipient" label="Recipient Address"></q-input>
          <q-input v-model="amount" label="Amount"></q-input>
          <q-select v-model="selectedCurrency" :options="currencies" label="Currency"></q-select>
          <q-input v-model="description" label="Description"></q-input>
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Send" @click="sendConfirm = true" />
          <q-btn flat label="Request" @click="requestConfirm = true" />
          <q-btn flat label="Close" v-close-dialog />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="sendConfirm" persistent transition-show="scale" transition-hide="scale">
      <q-card class="bg-teal text-white" style="width: 300px">
        <q-card-section>
          <div class="text-h6">Are you sure you want to Send?</div>
        </q-card-section>

        <q-card-actions align="right" class="bg-white text-teal">
          <q-btn flat label="YES" v-close-dialog />
          <q-btn flat label="NO" v-close-dialog />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="requestConfirm" persistent transition-show="scale" transition-hide="scale">
      <q-card class="bg-teal text-white" style="width: 300px">
        <q-card-section>
          <div class="text-h6">Are you sure you want to Request?</div>
        </q-card-section>

        <q-card-actions align="right" class="bg-white text-teal">
          payeeKey, payeePrivateKey, payerKey, currency, amount
          <q-btn flat label="YES" @click="createTransaction(
            recipient, walletAddress, selectedCurrency, amount
          )"/>
          <q-btn flat label="NO" v-close-dialog />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
export default {
  name: 'Transaction',
  props: ['walletAddress'],
  data: function () {
    return {
      sendConfirm: false,
      requestConfirm: false,
      currencies: ['BTC', 'ETH'],
      selectedCurrency: '',
      recipient: '',
      amount: '',
      description: '',
      isTransacting: false
    }
  }
}
</script>
