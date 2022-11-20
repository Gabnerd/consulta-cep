<script setup>
import { ref, reactive } from 'vue';
import axios from 'axios';

const cepInput = ref('');
const loading = ref(false);
const onError = ref(false);
const msgError = ref('');
const responseCep = reactive({ bairro: '', cep: '', localidade: '', logradouro: '', uf: '' });

const consultar = () => {
  loading.value = true;
  onError.value = false;
  responseCep.bairro = "";
  responseCep.cep = "";
  responseCep.uf = "";
  responseCep.localidade = "";
  responseCep.logradouro = "";
  if (cepInput.value.length == 9) {
    axios({ url: `https://viacep.com.br/ws/${cepInput.value.replace('-', '')}/json/` }).then((res) => {
      if (!res.data.erro) {
        responseCep.bairro = res.data.bairro;
        responseCep.cep = res.data.cep;
        responseCep.uf = res.data.uf;
        responseCep.localidade = res.data.localidade;
        responseCep.logradouro = res.data.logradouro;
      } else {
        msgError.value = 'CEP invalido';
        onError.value = true;
      }
    }).catch(error => {
      msgError.value = 'Um erro Ocorreu, Tente novamente';
      onError.value = true;
      console.error(error);
    }).finally(() => {
      loading.value = false;
      cepInput.value = '';
    });
  } else {
    loading.value = false;
    onError.value = true;
    msgError.value = "CEP deve conter 8 numeros, revise o CEP e tente novamente";
  }
}

const formatcepinput = () => {
  cepInput.value = cepInput.value.replace('-', '').slice(0, 5) + "-" + cepInput.value.replace('-', '').slice(5);
}
</script>

<template>
  <div class="mx-auto w-full mt-10 flex flex-col">
    <div class="flex justify-center items-center ">
      <input type="text" v-model="cepInput" class="rounded-l-full h-10 w-1/2 md:w-3/12" @change="formatcepinput()">
      <button @click="consultar()"
        class="rounded-r-full bg-slate-800 text-white h-10 w-1/3 md:w-1/6 flex items-center justify-center"><svg
          xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5 mr-2">
          <path fill-rule="evenodd"
            d="M9 3.5a5.5 5.5 0 100 11 5.5 5.5 0 000-11zM2 9a7 7 0 1112.452 4.391l3.328 3.329a.75.75 0 11-1.06 1.06l-3.329-3.328A7 7 0 012 9z"
            clip-rule="evenodd" />
        </svg>
        consultar</button>
    </div>
    <div v-if="responseCep.cep != ''" class="md:w-5/12 mx-auto border rounded mt-5 px-5 py-2">
      <div class="flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
          <path d="M3 4a2 2 0 00-2 2v1.161l8.441 4.221a1.25 1.25 0 001.118 0L19 7.162V6a2 2 0 00-2-2H3z" />
          <path d="M19 8.839l-7.77 3.885a2.75 2.75 0 01-2.46 0L1 8.839V14a2 2 0 002 2h14a2 2 0 002-2V8.839z" />
        </svg>
        <p class="text-xl whitespace-nowrap" :class="responseCep.cep == '' ? 'text-red-900' : ''">
          CEP: {{ responseCep.cep != ''
              ? responseCep.cep : "não definido"
          }}</p>
      </div>
      <div class="flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
          <path fill-rule="evenodd"
            d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-1.503.204A6.5 6.5 0 117.95 3.83L6.927 5.62a1.453 1.453 0 001.91 2.02l.175-.087a.5.5 0 01.224-.053h.146a.5.5 0 01.447.724l-.028.055a.4.4 0 01-.357.221h-.502a2.26 2.26 0 00-1.88 1.006l-.044.066a2.099 2.099 0 001.085 3.156.58.58 0 01.397.547v1.05a1.175 1.175 0 002.093.734l1.611-2.014c.192-.24.296-.536.296-.842 0-.316.128-.624.353-.85a1.363 1.363 0 00.173-1.716l-.464-.696a.369.369 0 01.527-.499l.343.257c.316.237.738.275 1.091.098a.586.586 0 01.677.11l1.297 1.297z"
            clip-rule="evenodd" />
        </svg>
        <p class="text-xl" :class="responseCep.uf == '' ? 'text-red-900' : ''">Unidade Federal: {{ responseCep.uf !=
            '' ? responseCep.uf : "não definido"
        }}</p>
      </div>
      <div class="flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
          <path fill-rule="evenodd"
            d="M1 2.75A.75.75 0 011.75 2h10.5a.75.75 0 010 1.5H12v13.75a.75.75 0 01-.75.75h-1.5a.75.75 0 01-.75-.75v-2.5a.75.75 0 00-.75-.75h-2.5a.75.75 0 00-.75.75v2.5a.75.75 0 01-.75.75h-2.5a.75.75 0 010-1.5H2v-13h-.25A.75.75 0 011 2.75zM4 5.5a.5.5 0 01.5-.5h1a.5.5 0 01.5.5v1a.5.5 0 01-.5.5h-1a.5.5 0 01-.5-.5v-1zM4.5 9a.5.5 0 00-.5.5v1a.5.5 0 00.5.5h1a.5.5 0 00.5-.5v-1a.5.5 0 00-.5-.5h-1zM8 5.5a.5.5 0 01.5-.5h1a.5.5 0 01.5.5v1a.5.5 0 01-.5.5h-1a.5.5 0 01-.5-.5v-1zM8.5 9a.5.5 0 00-.5.5v1a.5.5 0 00.5.5h1a.5.5 0 00.5-.5v-1a.5.5 0 00-.5-.5h-1zM14.25 6a.75.75 0 00-.75.75V17a1 1 0 001 1h3.75a.75.75 0 000-1.5H18v-9h.25a.75.75 0 000-1.5h-4zm.5 3.5a.5.5 0 01.5-.5h1a.5.5 0 01.5.5v1a.5.5 0 01-.5.5h-1a.5.5 0 01-.5-.5v-1zm.5 3.5a.5.5 0 00-.5.5v1a.5.5 0 00.5.5h1a.5.5 0 00.5-.5v-1a.5.5 0 00-.5-.5h-1z"
            clip-rule="evenodd" />
        </svg>
        <p class="text-xl" :class="responseCep.bairro == '' ? 'text-red-900' : ''">Bairro: {{ responseCep.bairro !=
            '' ? responseCep.bairro : "não definido"
        }}</p>
      </div>
      <div class="flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
          <path fill-rule="evenodd"
            d="M9.69 18.933l.003.001C9.89 19.02 10 19 10 19s.11.02.308-.066l.002-.001.006-.003.018-.008a5.741 5.741 0 00.281-.14c.186-.096.446-.24.757-.433.62-.384 1.445-.966 2.274-1.765C15.302 14.988 17 12.493 17 9A7 7 0 103 9c0 3.492 1.698 5.988 3.355 7.584a13.731 13.731 0 002.273 1.765 11.842 11.842 0 00.976.544l.062.029.018.008.006.003zM10 11.25a2.25 2.25 0 100-4.5 2.25 2.25 0 000 4.5z"
            clip-rule="evenodd" />
        </svg>
        <p class="text-xl" :class="responseCep.localidade == '' ? 'text-red-900' : ''">Localidade:
          {{ responseCep.localidade != '' ? responseCep.localidade : "não definido" }}</p>
      </div>
      <div class="flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
          <path fill-rule="evenodd"
            d="M9.293 2.293a1 1 0 011.414 0l7 7A1 1 0 0117 11h-1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-3a1 1 0 00-1-1H9a1 1 0 00-1 1v3a1 1 0 01-1 1H5a1 1 0 01-1-1v-6H3a1 1 0 01-.707-1.707l7-7z"
            clip-rule="evenodd" />
        </svg>
        <p class="text-xl" :class="responseCep.logradouro == '' ? 'text-red-900' : ''">Logradouro:
          {{ responseCep.logradouro != '' ? responseCep.logradouro : "não definido" }}</p>
      </div>
    </div>
    <div class="mx-auto w-fit mt-5 text-red-800 bg-red-300 px-5 py-3 rounded" v-if="onError">
      {{ msgError }}
    </div>
    <div v-if="loading" class="w-1/2 mx-auto flex justify-center items-center">
      <svg class="animate-spin w-full md:w-1/6 mx-auto mt-10 text-indigo-700" xmlns="http://www.w3.org/2000/svg"
        fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
        </path>
      </svg>
    </div>
  </div>
</template>

<style scoped>

</style>
