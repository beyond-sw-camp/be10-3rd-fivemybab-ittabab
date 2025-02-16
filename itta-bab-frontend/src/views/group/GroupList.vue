<script setup>
import axios from "axios";
import {computed, onMounted, provide, ref} from "vue";
import {useRouter} from "vue-router";
import {useAuthStore} from "@/stores/auth.js";
import BottomPageButton from "@/components/common/BottomPageButton.vue";
import SearchBar from "@/components/common/SearchBar.vue";
import PageTitleTop from "@/components/common/PageTitleTop.vue";
import ReportButton from "@/components/common/ReportButton.vue";

// 로그인 사용자 정보
const authStore = useAuthStore();       // 로그인 토큰

// DB 데이터
const groupData = ref([]);        // DB 모임 데이터
const groupCategoryData = ref([]);  // DB 모임 카테고리 데이터

// 페이지 관련
const currentPage = ref(1);       // 현재 페이지
const itemsPage = 10;                   // 페이지 당 보여줄 데이터
const selectedItemId = ref({
  target: "GROUP",
  groupId: null
}); // 선택된 아이템의 ID를 저장

// 라우터
const router = useRouter();

// REST API 호출 함수
const fetchData = async () => {
  try {
    const response1 = await axios.get("http://localhost:8003/group/list", {
      headers: {
        Authorization: `Bearer ${authStore.accessToken}`
      }
    });
    groupData.value = response1.data;

    const response2 = await axios.get("http://localhost:8003/groupCategory", {
      headers: {
        Authorization: `Bearer ${authStore.accessToken}`
      }
    });
    groupCategoryData.value = response2.data;
    console.log(groupCategoryData);

  } catch (error) {
    console.error("어라라...?\n", error);
  }
};

function selectItem(groupId) {
  selectedItemId.value.groupId = groupId;
  console.log("신고 ID:", selectedItemId.value);
  console.log({
    name: 'ReportCreate',
    query: {
      target: selectedItemId.value.target,
      targetId: selectedItemId.value.groupId
    }
  });
  router.push({
    name: 'ReportCreate',
    query: {
      target: selectedItemId.value.target,
      targetId: selectedItemId.value.groupId
    }
  });
}

onMounted(() => {
  fetchData(); // 컴포넌트가 마운트될 때 데이터 가져오기
});

const totalPages = computed(() => {
  return Math.ceil(groupData.value.length / itemsPage);
});

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * itemsPage;
  const end = start + itemsPage;
  return groupData.value.slice(start, end); // 필터링된 데이터에서 페이지네이션
});

// 날짜 형식화 함수
function formatDate(dateString) {
  const date = new Date(dateString);
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0'); // 월은 0부터 시작하므로 +1
  const day = String(date.getDate()).padStart(2, '0');
  const hours = String(date.getHours()).padStart(2, '0');
  const minutes = String(date.getMinutes()).padStart(2, '0');
  return `${year}-${month}-${day} ${hours}:${minutes}`;
}

// 카테고리 이름 반환 함수
function getCategoryName(categoryId) {
  const category = groupCategoryData.value.find(item => item.groupCategoryId === categoryId);

  return category ? category.groupCategoryName : "기타";
}

function goToPage(page) {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page;
  }
}

function goToRegisterPage() {
  router.push("/group/register");
}

  function goToDetailPage(groupId) {
    router.push(`/group/${groupId}`);
  }

const filter = (searchTerm) => {
  if (searchTerm.trim() === "") { // 빈칸인지 확인
    fetchData(); // 검색어가 빈칸이면 전체 데이터를 다시 가져옴
    currentPage.value = 1; // 페이지를 1로 초기화
    return;
  }

  // 검색어가 포함된 항목만 필터링
  groupData.value = groupData.value.filter(item =>
      item.group_title.includes(searchTerm)
  );

  // 검색 후 현재 페이지를 1로 초기화
  currentPage.value = 1;
};

provide("filter", filter);
</script>

<template>
  <div class="background">
    <div class="title-container">
      <PageTitleTop/>
    </div>
    <div class="title">
      <h1>모임 리스트</h1>
    </div>
    <div class="search-container">
      <SearchBar @search="filter"/>
    </div>
    <div class="total-container">
      <div class="header-row">
        <div class="header-item">모임종류</div>
        <div class="header-item">제목</div>
        <div class="header-item">모집인원</div>
        <div class="header-item">마감시간</div>
        <div class="blank"></div>
      </div>

      <div class="list-style">
        <div
            v-for="item in paginatedData"
            :key="item.groupId"
            class="data-row"
            v-on:click="goToDetailPage(item.groupId)"
        >
          <div class="data-item">{{ getCategoryName(item.groupCategoryId) }}</div>
          <div class="data-item">{{ item.groupTitle }}</div>
          <div class="data-item">{{ item.userCounting }}</div>
          <div class="data-item">{{ formatDate(item.endDate) }}</div>
          <div class="report-button-container">
            <ReportButton @click.stop="selectItem(item.groupId)"/>
          </div>
        </div>
      </div>
    </div>

    <div class="bottom-container">
      <BottomPageButton
          :currentPage="currentPage"
          :totalPages="totalPages"
          @changePage="goToPage"
          @writePage="goToRegisterPage"
      >등록
      </BottomPageButton>
    </div>
  </div>
</template>


<style scoped>
.background {
  display: flex; /* Flexbox 사용 */
  flex-direction: column; /* 세로 방향으로 정렬 */
  justify-content: center; /* 수직 가운데 정렬 */
  align-items: center; /* 수평 가운데 정렬 */
  width: 100%;
  background-color: var(--background-color); /* 전체 배경색 */
  padding: 20px;
  border-radius: 10px; /* 모서리 둥글게 */
}

.search-container {
  width: 80%;
  margin-bottom: 12px;
}

.header-row {
  display: flex;
  background-color: var(--basic-yellow); /* 노란색 배경 */
  padding: 15px;
  border-radius: 10px 10px 0 0; /* 윗부분만 둥글게 */
  font-weight: bold;
}

.header-item {
  flex: 1;
  text-align: center;
}

.data-row {
  display: flex;
  padding: 15px;
  margin-bottom: 14px; /* 아래 여백 추가 */
  border-bottom: 1px solid #ddd; /* 가로줄 추가 */
  align-items: center; /* 세로 가운데 정렬 */
}

.data-item {
  flex: 1;
  text-align: center;
}

/* 신고 버튼을 위한 스타일 추가 */
.report-button-container {
  display: flex;
  justify-content: flex-end; /* 오른쪽 끝 정렬 */
  margin-left: auto; /* 왼쪽 여백 자동으로 설정하여 오른쪽 정렬 */
}


.page-named span {
  cursor: pointer;
  padding: 5px 10px;
  border: 1px solid var(--white);
  background-color: var(--white);
}

.page-named .active {
  font-weight: bold;
  color: black;
}

.list-style {
  background-color: white;
  border-radius: 0 0 10px 10px; /* 윗부분만 둥글게 */
}

.total-container {
  width: 80%;
}

.bottom-container {
  display: flex;
  justify-content: center; /* 가운데 정렬 */
  align-items: center; /* 세로 방향 가운데 정렬 */
  width: 80%; /* 너비를 설정하여 부모와 맞춤 */
}

.bottom-container button {
  justify-content: flex-end; /* 글쓰기 버튼을 오른쪽 끝 정렬 */
}


.blank {
  width: 45px;
}

</style>