<script setup>
import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome";
</script>

<template>
  <div class="container">
    <div class="yellow">
      <div class="text-container">
        익명 커뮤니티<div class="faArrow"><font-awesome-icon :icon="['fas', 'circle-arrow-right']" /></div>
      </div>
    </div>
    <div class="list">
      <div class="line">
        <div class="id">1123</div>
        <div class="title">피나치공 맛있더라</div>
        <div class="good">좋아요 10</div>
      </div>
      <div class="line">
        <div class="id">1123</div>
        <div class="title">피나치공 맛있더라</div>
        <div class="good">좋아요 10</div>
      </div>
      <div class="line">
        <div class="id">1123</div>
        <div class="title">피나치공 맛있더라</div>
        <div class="good">좋아요 10</div>
      </div>
    </div>
  </div>
</template>

<style scoped>

.yellow{
  border-radius: 20px 20px 0px 0px;
  background-color: var(--basic-yellow);
  height: 72px;
  text-align: left;
  padding-left: 30px;
  display: flex;
  justify-content: center;
  flex-direction: column;
  font-size: 20px;
  font-weight: 600;
  width: 414px;
}
.text-container {
  display: flex;
  align-items: center;
}
.faArrow {
  margin : 10px;
}
.list{
  background-color: var(--white);
  border-radius: 0px 0px 20px 20px;
  height: 513px;
  padding: 32px 0px;
  display: flex;
  flex-direction: column;
}
.line{
  margin: 10px 42px;
  border-bottom: 2px var(--gray-font) solid;
  padding-bottom: 4px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.id, .title, .good {
  font-size: 12px;
  font-weight: 400;
}
</style>