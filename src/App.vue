<template>
  <div>
    <h1>ED25519 Page</h1>

    <div>
      <p>Private Key</p>
      <input v-model="privateKey" type="text" style="width: 450px" />
      <button @click="randomPrivateKey" style="margin-left: 30px">
        Random Key
      </button>
      <button @click="generatePrivateKey" style="margin-left: 30px">
        Generate from seed
      </button>
    </div>
    <div>
      <p>Public Key</p>
      <input v-model="publicKey" type="text" style="width: 450px" />
    </div>
    <div>
      <p>Message</p>
      <input v-model="message" type="text" style="width: 450px" />
      <button @click="sign" style="margin-left: 30px">Sign</button>
    </div>
    <div>
      <p>Signature</p>
      <input v-model="signature" type="text" style="width: 450px" />
      <button @click="verify" style="margin-left: 30px">Verify</button>
    </div>
    <div>
      <p>Status</p>
      <p>{{ this.status }}</p>
    </div>
  </div>
</template>

<script>
import * as ed from "@noble/ed25519";
export default {
  name: "App",
  data() {
    return {
      privateKey: "",
      publicKey: "",
      message: "Hello world!",
      signature: "",
      status: "",
    };
  },
  methods: {
    async randomPrivateKey() {
      const key = ed.utils.randomPrivateKey();
      this.privateKey = Buffer.from(key).toString("hex");
      this.publicKey = Buffer.from(
        await ed.getPublicKey(this.privateKey)
      ).toString("hex");
      this.signature = ''
    },
    async generatePrivateKey() {
      try {
        this.publicKey = Buffer.from(
          await ed.getPublicKey(this.privateKey)
        ).toString("hex");
        this.signature = ''
      } catch (e) {
        alert(`Error: ${e}`);
      }
    },
    async sign() {
      const sig = await ed.sign(this.toHex(this.message), this.privateKey);
      this.signature = Buffer.from(sig).toString("hex");
    },
    async verify() {
      const isValid = await ed.verify(
        this.signature,
        this.toHex(this.message),
        this.publicKey
      );
      this.status =  isValid;
    },
    toHex(str) {
      var result = "";
      for (var i = 0; i < str.length; i++) {
        result += str.charCodeAt(i).toString(16);
      }
      return result;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
