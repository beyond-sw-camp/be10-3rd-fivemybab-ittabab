<script setup>
import {ref} from 'vue';
import {useRoute, useRouter} from 'vue-router'; // useRouter import
import '@/assets/css/resetcss.css';
import axios from "axios";
import {useAuthStore} from "@/stores/auth.js";

const userInput = ref('');
const errorMessage = ref('');
const router = useRouter(); // useRouter 사용
const route = useRoute();
const authStore = useAuthStore();

const checkInput = async () => {
  try {

    if (userInput.value !== '회원 탈퇴 하겠습니다.') {
      errorMessage.value = '정확히 "회원 탈퇴 하겠습니다."를 입력해주세요.';
    } else {
      errorMessage.value = '';

      const response = await axios.delete('http://localhost:8003/user/mypage', {
        headers: {
          Authorization: `Bearer ${authStore.accessToken}`,
        },
      });

      router.push({
        path: '/delete-user-result',
        query: {
          username: route.query.username,
        }
      });
    }

  } catch (error) {
    console.error('회원정보 삭제 실패', error);
  }
};

const handleEnter = (event) => {
  if (event.key === 'Enter') {
    checkInput();
  }
};


</script>


<template>
  <div class="back">
    <img src="@/assets/icons/logo.svg">
    <div class="white-box">
      <div class="title">회원탈퇴</div>
      <div class="form">
        <input type="text" v-model="userInput" placeholder="회원 탈퇴 하겠습니다.(따라서 작성하세요)" @keydown="handleEnter">
      </div>
      <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
      <input type="button" value="탈퇴하기" @click="checkInput">
    </div>
  </div>
</template>

<style scoped>
input::placeholder {
  text-align: center;
  color: var(--gray-font);
}

input[type="text"] {
  height: 70px;
  background-color: var(--gray-input);
  border: none;
  border-radius: 10px;
  width: 670px;
  padding: 0px 60px;
}

input[type="button"] {
  margin-top: 30px;
  border-radius: 52px;
  background-color: var(--basic-yellow);
  border: none;
  font-weight: 600;
  font-size: 18px;
  width: 30%;
  height: 44px;
  filter: drop-shadow(2px 2px 4px rgba(0, 0, 0, 0.50));
}

.title {
  font-weight: 600;
  font-size: 25px;
  padding-bottom: 80px;
}

.white-box {
  background-color: var(--white);
  box-shadow: 0px 4px 13px 0px rgba(0, 0, 0, 0.13) inset;
  border-radius: 43px;
  height: 500px;
  width: 60%;
  margin-top: 50px;
  display: flex;
  padding-top: 60px;
  align-items: center;
  flex-direction: column;
}

.back img {
  width: 66px;
}

.back {
  background-color: var(--background-color);
  display: flex;
  justify-content: center;
  padding-top: 100px;
  flex-direction: column;
  align-items: center;
}

.error-message {
  color: var(--real-red);
  font-size: 16px;
  margin-top: 10px;
}
</style>