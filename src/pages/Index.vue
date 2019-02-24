<template>
  <q-page class='flex flex-center'>
    <q-page-sticky position="top-right" :offset="[18, 18]">
      <Notifications />
    </q-page-sticky>
    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <Transaction :walletAddress="walletAddress" />
    </q-page-sticky>
  </q-page>
</template>

<style>
</style>

<script>
/* eslint-disable no-console */

import Portis from '@portis/web3'
import Web3 from 'web3'
import {
  // Request,
  RequestNetwork,
  Types
} from '@requestnetwork/request-client.js'
import { EthereumPrivateKeySignatureProvider } from '@requestnetwork/epk-signature'
import Transaction from '../components/Transaction'
import Notifications from '../components/Notifications'

const DAPP_ID = '426fc4a3-d87e-4381-a6eb-8134b254b3ed'

export default {
  name: 'PageIndex',
  data: function () {
    return {
      user: null,
      walletAddress: null
    }
  },
  components: {
    Transaction,
    Notifications
  },
  mounted: function () {
    const portis = new Portis(
      DAPP_ID,
      'rinkeby' // 'mainnet'
    )
    const web3 = new Web3(portis.provider)

    web3.eth.getAccounts((error, accounts) => {
      if (error) {
        console.log('Error: Cant get account')
      } else {
        this.walletAddress = accounts[0]
        this.createRequest()
      }
    })

    // this.user = ''
    /*
      // store/get data
      this.$q.localStorage.set(key, value)
      let value = this.$q.localStorage.getItem(key)
      this.$q.sessionStorage.set(key, value)
      let value = this.$q.sessionStorage.getItem(key)
      */

    // web3.currentProvider.isPortis //returns boolean if user is logged into Portis
  },
  methods: {
    createRequest () {
      // Payee Identity and Signature parameters

      const payeeIdentity = {
        type: Types.Identity.TYPE.ETHEREUM_ADDRESS,
        value: '0xbab6575ce34db3432dc501c142eb4a3f7ffd4a65'
      }

      const payeeSignatureParameters = {
        method: Types.Signature.METHOD.ECDSA,
        privateKey:
          '0x18ae62fa934cc62ec5e48df038b3dcaa9b927227caaa6f7d05108c7f1373febc'
      }

      // Payer Identity

      const payerIdentity = {
        type: Types.Identity.TYPE.ETHEREUM_ADDRESS,
        value: '0xf64Fc7Db7FeD935ad167c268686F4DB7FB966467'
      }

      // Request basic information

      const requestCreationHash = {
        currency: Types.RequestLogic.CURRENCY.BTC,
        expectedAmount: '1',

        // payee is not mandatory if payer given
        payee: payeeIdentity,
        // payer is not mandatory if payee given
        payer: payerIdentity
      }

      // Payment network to reconciliate the payment

      const paymentNetwork = {
        // Here testnet bitcoin is used
        id: Types.PAYMENT_NETWORK_ID.TESTNET_BITCOIN_ADDRESS_BASED,
        parameters: {
          paymentAddress: '2NBvyZCCX7FaMD4cJMW1hm1XAbzQEfUDxDn'
        }
      }

      // Some arbitrary content to link to the request (you can use request-data-format here)

      const contentData = {
        it: 'is',
        some: 'content',
        true: true
      }

      // Automatically the requestId, the payee identity value and the payer identity value are added to the topics

      // But you can index your request with your extra topics

      const topics = [
        '0xbab6575ce34db3432dc501c142eb4a3f7ffd4a65',
        '0xf64Fc7Db7FeD935ad167c268686F4DB7FB966467',
        '2NBvyZCCX7FaMD4cJMW1hm1XAbzQEfUDxDn'
      ]

      // Signature provider will take care of signing everything

      const signatureProvider = new EthereumPrivateKeySignatureProvider(
        payeeSignatureParameters
      )

      // Create the requestNetwork object

      // const requestNetwork = new RequestNetwork({
      //   useMockStorage: true,
      //   signatureProvider
      // })

      const requestNetwork = new RequestNetwork({
        useMockStorage: true,
        signatureProvider
      })

      const foo = async () => {
        // Create a request
        const request = await requestNetwork.createRequest({
          contentData,
          paymentNetwork,
          requestInfo: requestCreationHash,
          signer: payeeIdentity,
          topics
        })

        const requestData = await request.getData()
        console.log('requestId', requestData.requestId)
        console.log('creator of the request identity:', requestData.creator)
        console.log('payee identity:', requestData.payee)
        console.log('payer identity:', requestData.payer)
        console.log('expected amount:', requestData.expectedAmount)
        console.log('state (created, accepted, canceled):', requestData.state)
        console.log('historical events:', requestData.events)

        // not necessary given, only indication given by the request payer
        console.log('arbitrary timestamp:', requestData.timestamp)

        // Balance information - null if no payment network used
        if (requestData.balance) {
          console.log(
            'Current balance on the request:',
            requestData.balance.balance
          )

          console.log(
            'History of payments and refunds:',
            requestData.balance.events
          )
        }

        console.log('Content data:', requestData.contentData)
        // Some meta data about the request (where the data are stored for example)
        console.log('meta:', requestData.meta)
        /*
      // payee information
      const payeeSignatureInfo = {
        method: RequestNetwork.Types.Signature.METHOD.ECDSA,
        privateKey: '0x18ae62fa934cc62ec5e48df038b3dcaa9b927227caaa6f7d05108c7f1373febc'
      }
      const payeeIdentity = {
        type: RequestNetwork.Types.Identity.TYPE.ETHEREUM_ADDRESS,
        value: '0xbab6575ce34db3432dc501c142eb4a3f7ffd4a65'
      }

      // Signature providers
      const signatureProvider = new EthereumPrivateKeySignatureProvider(payeeSignatureInfo)

      const requestInfo = {
        currency: RequestNetwork.Types.RequestLogic.REQUEST_LOGIC_CURRENCY.ETH,
        expectedAmount: '0.01',
        payee: payeeIdentity,
        payer: {
          type: RequestNetwork.Types.Identity.TYPE.ETHEREUM_ADDRESS,
          value: '0xf64Fc7Db7FeD935ad167c268686F4DB7FB966467'
        }
      }

      const topics = [
        '0xbab6575ce34db3432dc501c142eb4a3f7ffd4a65',
        '0xf64Fc7Db7FeD935ad167c268686F4DB7FB966467',
        'all'
      ]

      const paymentNetwork = {
        id: RequestNetwork.Types.PAYMENT_NETWORK_ID.ETHEREUM_ADDRESS_BASED,
        parameters: {
          paymentAddress: '0xbab6575ce34db3432dc501c142eb4a3f7ffd4a65'
        }
      }

      (async () => {
        const requestNetwork = new RequestNetwork.RequestNetwork({
          signatureProvider, nodeConnectionConfig: { baseURL: 'http://localhost:3000' }
        })

        const request = await requestNetwork.createRequest({
          paymentNetwork,
          requestInfo,
          signer: payeeIdentity,
          topics
        })

        console.log(request)
      })()
      */
      }
      foo()
    }
  }
}
</script>
