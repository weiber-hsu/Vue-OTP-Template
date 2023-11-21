<script lang="ts" setup>
/*
-[ ] able to paste code from clipboard
*/
import { onMounted, ref, watch } from 'vue';
import axios from 'axios';
const emit = defineEmits(['verifyStatus'])
const code = ref(['', '', '', '']);
const loading = ref(false);
const verifyError = ref(false) 

const verifyCode = async () => {
  loading.value = true
  verifyError.value = false
  await axios.post('/api/verify', {
    "code": code.value.join('')
  })
  .then(function(response){
    if(response.data.valid === true){
      localStorage.setItem("LoginStatus", JSON.stringify(response.data.token))
      emit("verifyStatus")
    }
    else{
      verifyError.value = true
    }
    loading.value = false;
  });
}
const onKeyDown = (i, event) =>{
  if(event.key === 'Backspace' && i > 0){
    event.preventDefault();
    if(code.value[i] === ''){
      code.value[i-1] = ''
      document.getElementById(`id-${i}`).focus();
    }
    else{
      code.value[i] = ''
    }
    return
  }
}

const handleInput = (i, event) => {
  if(event.key === 'Backspace'){
    event.preventDefault();
  }
  code.value[i] = code.value[i].replace(/\D/g, '');
  if (/^\d+$/.test(code.value[i]) && i+2<5) {
    document.getElementById(`id-${i + 2}`).focus();
  }

  if(code.value.every(item => item !== '')){
    verifyCode()
  }
};

watch(code, (newVal, oldVal) => {
  newVal.forEach((value, i) => {
    document.getElementById(`id-${i + 1}`).value = value;
  });
});

onMounted(async () => {
  document.getElementById('id-1').focus()
});

</script>
<template>
    <form>
      <div class="otp-container">
        <h1 v-if="loading">Loading...</h1>
        <h1 v-if="verifyError">Verify Error</h1>
        <h3 class="otp-title">Enter verification</h3>
        <div class="input-group">
          <input v-for="i in 4" :key="'id-' + i"
            class="input"
            v-model="code[i-1]"
            maxlength="1"
            type="text"
            :id="'id-' + i"
            @keydown="onKeyDown( i-1 , $event)"
            @input="handleInput(i-1, $event)"
          />
        </div>
      </div>
    </form>
</template>
<style scoped>
.otp-container {
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  gap: 1rem;
}
.otp-title {
  font-size: 2rem;
}

.input-group {
  display: flex;
  gap: 1rem;
}

.input-group > .input {
  width: 8rem;
  height: 12rem;
  text-align: center;
  font-size: 2rem;
}
</style>
