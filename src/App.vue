<script setup lang="ts">
import OtpVerify from '@/components/OtpVerify.vue'
import Profile from '@/components/Profile.vue'
import { verify } from 'crypto';
import { onMounted, ref } from 'vue';
import axios from 'axios';

const isVerified = ref(JSON.parse(localStorage.getItem("LoginStatus")));

const VerifyStatus = async () =>{
  await axios.get('/api/auth', {
    headers:{
      "Authorization": JSON.parse(localStorage.getItem("LoginStatus"))
    }
  })
  .then(function(response){
    if(response.data.message){
      isVerified.value = false
      return false
    }
    else{
      isVerified.value = true
      return true
    }
  });

}
</script>

<template>
  <Profile v-if="isVerified" @LogOut="VerifyStatus"/>
  <OtpVerify v-else @verifyStatus="VerifyStatus" />
</template>
