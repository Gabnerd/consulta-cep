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
  if(cepInput.value.length == 9){
  axios({ url: `https://viacep.com.br/ws/${cepInput.value.replace('-', '')}/json/` }).then((res) => {
    responseCep.bairro = res.data.bairro;
    responseCep.cep = res.data.cep;
    responseCep.uf = res.data.uf;
    responseCep.localidade = res.data.localidade;
    responseCep.logradouro = res.data.logradouro;
  }).catch(error=>{
    msgError.value =  'Um erro Ocorreu, Tente novamente';
    onError.value = true;
    console.error(error);
  }).finally(()=>{
    loading.value = false;
    cepInput.value = '';
  });
}else{
  loading.value = false;
  onError.value = true;
  msgError.value = "CEP deve conter 8 numeros, revise o CEP e tente novamente";
}
}

const formatcepinput = () => {
  //99999-999
  cepInput.value = cepInput.value.replace('-', '').slice(0, 5) + "-" + cepInput.value.replace('-', '').slice(5);
}
</script>

<template>
  <div class="mx-auto w-full mt-10">
    <div class="flex justify-center items-center ">
        <input type="text" v-model="cepInput" class="rounded-l-full h-10 w-1/2 md:w-1/5" @change="formatcepinput()"><button
          @click="consultar()" class="rounded-r-full bg-slate-800 text-white h-10 w-1/3 md:w-1/6">consultar</button>
    </div>
    <div v-if="responseCep.uf != ''">
      <p class="text-xl" :class="responseCep.cep == undefined ? 'text-red-900' : ''">CEP: {{ responseCep.cep != undefined
          ? responseCep.cep : "não definido"
      }}</p>
      <p class="text-xl" :class="responseCep.uf == undefined ? 'text-red-900' : ''">Unidade Federal: {{ responseCep.uf !=
          undefined ? responseCep.uf : "não definido"
      }}</p>
      <p class="text-xl" :class="responseCep.bairro == undefined ? 'text-red-900' : ''">Bairro: {{ responseCep.bairro !=
          undefined ? responseCep.bairro : "não definido"
      }}</p>
      <p class="text-xl" :class="responseCep.localidade == undefined ? 'text-red-900' : ''">Localidade:
        {{ responseCep.localidade != undefined ? responseCep.localidade : "não definido" }}</p>
      <p class="text-xl" :class="responseCep.logradouro == undefined ? 'text-red-900' : ''">Logradouro:
        {{ responseCep.logradouro != undefined ? responseCep.logradouro : "não definido" }}</p>
    </div>
    <div class="mx-auto w-fit mt-5 text-red-800 bg-red-300 px-5 py-3 rounded" v-if="onError">
      {{msgError}}
    </div>
    <div v-if="loading" class="w-1/2 mx-auto flex justify-center items-center">
    <svg  class="animate-spin w-full md:w-1/6 mx-auto mt-10 text-indigo-700" xmlns="http://www.w3.org/2000/svg"
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
