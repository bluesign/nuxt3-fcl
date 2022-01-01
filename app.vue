<template>
  <div>
  <button @click=fcl.logIn>Login</button>

  <button @click=executeTransaction>Transaction</button>
  </div>
</template>

<script setup>

import * as fcl from "@bluesign/fcl"
import {config} from "@bluesign/fcl"


config({
  "accessNode.api": "https://access-testnet.onflow.org",
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",
  "0xProfile": "0xba1132bc08f82fe2" // The account address where the smart contract lives
})

const executeTransaction = async () => {
  console.log("transaction")
    const transactionId = await fcl.mutate({
      cadence: `
        import Profile from 0xProfile
        transaction(name: String) {
          prepare(account: AuthAccount) {
            account
              .borrow<&Profile.Base{Profile.Owner}>(from: Profile.privatePath)!
              .setName(name)
          }
        }
      `,
      args: (arg, t) => [arg("Flow Developer!", t.String)],
      payer: fcl.authz,
      proposer: fcl.authz,
      authorizations: [fcl.authz],
      limit: 50
    })

    fcl.tx(transactionId).subscribe(res => console.log(res.status))
  }

</script>