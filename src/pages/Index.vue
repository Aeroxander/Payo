<template>
  <q-page class='flex flex-center'>
    <q-page-sticky position="top-right" :offset="[18, 18]">
      <div class="q-pa-md q-gutter-sm">
        <q-btn fab color="warning" @click="showNotifications = true" :disabled="!notifications.length" >
          <q-icon name="far fa-bell"/>
        </q-btn>
      </div>
    </q-page-sticky>
    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <div class="q-pa-md q-gutter-sm">
        <q-btn fab icon="attach_money" color="accent" @click="transaction = true" />
        <PayDialog :transaction="transaction" :walletAddress="walletAddress"/>
      </div>
    </q-page-sticky>
  </q-page>
</template>

<style>
</style>

<script>
/* eslint-disable no-console */

import Portis from '@portis/web3'
import Web3 from 'web3'
import * as RequestNetwork from '@requestnetwork/request-client.js'
import { EthereumPrivateKeySignatureProvider } from '@requestnetwork/epk-signature'
import PayDialog from '../components/PayDialog'

const DAPP_ID = '426fc4a3-d87e-4381-a6eb-8134b254b3ed'

export default {
  name: 'PageIndex',
  data: function () {
    return {
      user: null,
      walletAddress: null,
      transaction: false,
      showNotifications: false,
      notifications: []
    }
  },
  components: {
    PayDialog
  },
  mounted: function () {
    /*
      const localNode = {
        nodeUrl: 'https://localhost:8545',
        chainId: 50,
        nodeProtocol: 'rpc'
      }
      */
    const portis = new Portis(
      DAPP_ID,
      'mainnet' // localNode
    )
    const web3 = new Web3(portis.provider)

    web3.eth.getAccounts((error, accounts) => {
      if (error) {
        console.log('Error: Cant get account')
      } else {
        this.walletAddress = accounts[0]
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
  createRequest () {
    // payee information
    const payeeSignatureInfo = {
      method: RequestNetwork.Types.Signature.METHOD.ECDSA,
      privateKey: '0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3'
    }
    const payeeIdentity = {
      type: RequestNetwork.Types.Identity.TYPE.ETHEREUM_ADDRESS,
      value: this.walletAddress
    }

    // Signature providers
    const signatureProvider = new EthereumPrivateKeySignatureProvider(payeeSignatureInfo)

    const requestInfo = {
      currency: RequestNetwork.Types.RequestLogic.REQUEST_LOGIC_CURRENCY.BTC,
      expectedAmount: '100000000000',
      payee: payeeIdentity,
      payer: {
        type: RequestNetwork.Types.Identity.TYPE.ETHEREUM_ADDRESS,
        value: '0x740fc87Bd3f41d07d23A01DEc90623eBC5fed9D6'
      }
    }

    const topics = [
      this.walletAddress,
      '0x740fc87Bd3f41d07d23A01DEc90623eBC5fed9D6'
    ]

    const paymentNetwork = {
      id: RequestNetwork.Types.PAYMENT_NETWORK_ID.BITCOIN_ADDRESS_BASED,
      parameters: {
        paymentAddress: 'mgPKDuVmuS9oeE2D9VPiCQriyU14wxWS1v'
      }
    };

    (async () => {
      const requestNetwork = new RequestNetwork.RequestNetwork({
        signatureProvider
      })

      const request = await requestNetwork.createRequest({
        paymentNetwork,
        requestInfo,
        signer: payeeIdentity,
        topics
      })

      console.log(request)
    })()
  }
}
</script>
