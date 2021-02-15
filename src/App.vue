<template>
  <div class="w-full h-screen flex items-center justify-center">
    <div class="flex flex-col max-w-screen-md w-full">
      <h1 class="font-bold text-2xl text-yellow-600">Bookique API tester</h1>
      <div class="mt-5 flex flex-col">
        <p>
          Impostare l'indirizzo completo dell'applicazione Bookique, nello
          stesso modo in cui normalmente accedete da browser.
        </p>
        <p>
          L'indirizzo a cui verrà fatta la richiesta è il seguente:
        </p>
        <pre class="mx-auto my-5 ">
            {{ apiUrl }}
          </pre
        >
        <p class="mb-2">
          Modificare opportunamente le variabili qui sotto per far coincidere la
          richiesta con l'URL dell'applicazione.
        </p>
        <p class="mb-2">
          <span class="text-red-800 font-bold">
            Attenzione!
          </span>
          <br />
          Il test verrà considerato valido se
        </p>
        <ul>
          <li>1. Si otterrà risposta dal server</li>
          <li>2. La risposta conterrà il seguente oggetto: { 'test': 'ok' }</li>
        </ul>
        <div class="grid grid-cols-3 mb-5 flex items-center mt-8">
          <label for="">Host</label>
          <input
            class="col-span-2 rounded border-yellow-400 border p-2"
            type="text"
            v-model="host"
          />
        </div>
        <div class="grid grid-cols-3 flex items-center mb-5">
          <label for="">Port</label>
          <input
            class="col-span-2 rounded border-yellow-400 border p-2"
            type="number"
            v-model="port"
          />
        </div>
        <div class="grid grid-cols-3 flex items-center mb-5">
          <label for="">Base Path</label>
          <input
            class="col-span-2 rounded border-yellow-400 border p-2"
            type="text"
            v-model="basePath"
          />
        </div>
        <div class="flex mt-4 justify-start">
          <button
            :disabled="btnDisabled"
            class="px-3 py-2 rounded"
            :class="{
              'bg-yellow-400 cursor-pointer': !btnDisabled,
              'bg-gray-300 cursor-not-allowed': btnDisabled
            }"
            @click="testApi()"
          >
            Test API
          </button>
        </div>
      </div>
      <div v-if="error" class="mt-8">
        <h2 class="text-red-500 font-bold text-xl">{{ error }}</h2>
        <p class="text-red-400">{{ errorLog }}</p>
      </div>
      <div v-if="success" class="mt-8">
        <h2 class="text-green-500 font-bold text-xl">{{ success }}</h2>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      host: "localhost",
      port: "80",
      basePath: "bookique",
      success: null,
      error: null,
      errorLog: null
    };
  },
  methods: {
    testApi() {
      this.error = null;
      this.errorLog = null;
      this.success = null;

      axios
        .get(this.apiUrl)
        .then(response => {
          let responseData = response.data;

          if (responseData && responseData.test && responseData.test == "ok") {
            this.success = "Test passato con successo";
          } else {
            this.error = "Test non andato a buon fine";
            this.errorLog =
              JSON.stringify(response.data) + " != {'test': 'ok'}";
          }
        })
        .catch(error => {
          this.error = "Test non andato a buon fine!";
          this.errorLog = error;
        });
    }
  },
  computed: {
    apiUrl() {
      return `http://${this.host}:${this.port}/${this.basePath}/api/test`;
    },
    btnDisabled() {
      return !this.host || !this.port || !this.basePath;
    }
  }
};
</script>
