<template>
  <div class="container mx-auto">
    <div class="mt-8">
      <h1 class="text-5xl">Asymmetric Encryption Example</h1>
    </div>

    <div
      class="bg-gray-200 rounded-xl shadow border p-8 m-10 flex items-center justify-between"
    >
      <div class="mr-8">
        <div class="mb-8">
          <label
            class="block text-gray-700 text-sm font-bold mb-2"
            for="public-key"
            >Your public key</label
          >
          <textarea name="public-key" v-model="publicKey" rows="4" cols="50" />
        </div>

        <div class="mb-8">
          <input type="file" @change="upload1" />
        </div>

        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          @click="encrypt"
        >
          ENCRYPT
        </button>
      </div>

      <div>
        <img :src="src1" />
      </div>
    </div>

    <div
      class="bg-gray-200 rounded-xl shadow border p-8 m-10 flex items-center justify-between"
    >
      <div class="mr-8">
        <div class="mb-8">
          <label
            class="block text-gray-700 text-sm font-bold mb-2"
            for="private-key"
            >Your private key</label
          >
          <textarea
            name="private-key"
            v-model="privateKey"
            rows="4"
            cols="50"
          />
        </div>

        <div class="mb-8">
          <label
            class="block text-gray-700 text-sm font-bold mb-2"
            for="private-key"
            >Your encrypted JSON</label
          >
          <textarea name="private-key" v-model="encrypted" rows="4" cols="50" />
        </div>

        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          @click="decrypt"
        >
          DECRYPT
        </button>
      </div>

      <div>
        <img :src="src2" />
      </div>
    </div>

    <div>
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="createKeys"
      >
        CREATE KEYS
      </button>
    </div>
  </div>
</template>

<script>
import { Crypt, RSA } from "hybrid-crypto-js";

const convertBase64 = (file) => {
  return new Promise((resolve) => {
    const fileReader = new FileReader();
    fileReader.readAsDataURL(file);
    fileReader.onload = () => resolve(fileReader.result);
  });
};

const crypt = new Crypt();
const rsa = new RSA();

export default {
  name: "AppMain",
  data() {
    return {
      src1: null,
      src2: null,
      encrypted: null,
      publicKey: null,
      privateKey: null,
    };
  },
  methods: {
    async createKeys() {
      const { publicKey, privateKey } = await rsa.generateKeyPairAsync();
      this.publicKey = publicKey;
      this.privateKey = privateKey;
    },
    async upload1(e) {
      this.src1 = await convertBase64(e.target.files[0]);
    },
    async encrypt() {
      this.encrypted = crypt.encrypt(this.publicKey, this.src1);
    },
    async decrypt() {
      const decrypted = crypt.decrypt(this.privateKey, this.encrypted);
      this.src2 = decrypted.message;
    },
  },
};
</script>
