<template>
  <div class="container top ">
    <div class="profile-box d-flex flex-column align-items-center justify-content-center shadow-sm">
      <div class="profile-box-item">
        <img class="profile-image" src="@/assets/image/profile.png" alt="프로필">
      </div>
      <div class="profile-nickname">
        {{ userStore.user.nickname }}
      </div>
      <div class="follow-info">
        <span class="mx-1">팔로워</span>
        <span class="mx-1">{{ userStore.followerList.length }}</span>
        |
        <span class="mx-1">팔로잉</span>
        <span class="mx-1">{{ userStore.followingList.length }}</span>
      </div>
      <div class="profile-box-item">
        <button class="btn btn-outline-secondary mt-2" @click="goSetting">설정</button>
      </div>

      <hr>

      <div class="icons d-flex align-items-center profile-box-item">
        <div class="icon-group d-flex flex-column align-items-center">
          <RouterLink class="unline icon" :to="{ name: 'scrapbook' }"> <i class="bi bi-bookmark"> </i></RouterLink>
          <div class="icon-title">스크랩북</div>
          <div class="icon-num">{{ userStore.storyScrapList.length }}</div>
        </div>
        <div class="icon-group d-flex flex-column align-items-center">
          <RouterLink class="unline icon" :to="{ name: 'zzim' }"> <i class="bi bi-heart"></i> </RouterLink>
          <div class="icon-title">찜</div>
          <div class="icon-num">{{ userStore.productScrapList.length }}</div>
        </div>
      </div>
    </div>
    <div class="mystory">
      <div>
        <div class="section">
          <div class="section-title">
            <div class="small-title">
              <h4>나의 게시글 스크랩북</h4>
            </div>
          </div>


          <div class="popular d-flex align-items-start">
            <div class="no-story" v-if="noStory">
              <p>아직 작성한 게시글이 없어요.😅</p>
            </div>
            <!-- v-for 넣기 -->
            <div v-for="story in storyScrapList" :key="story.storyId" v-else>
              <div class="popular-pic">
                <img class="pic" :src="`src/assets/image/story/${story.image}`" alt="이미지"
                  @click="storyDetail(story.storyId)">
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="seller-regist d-flex justify-content-center" v-if="isUser">
      <div class="under-box">
        <button class="btn btn-outline-secondary btn-regist" @click="goSellerRegist">판매자 신청하기</button>
      </div>
    </div>
    <div class="seller-regist d-flex justify-content-center" v-else-if="isSeller">
      <div class="under-box">
        <button class="btn btn-outline-secondary btn-regist" @click="goProductRegist">판매 상품 등록하기</button>
      </div>
    </div>
    <!-- 유저가 셀러일 때 내 상품 표시? 일단 보류 -->
    <!-- <div class="mystory">
      <div >
        <div class="section">
          <div class="section-title">
            <div class="small-title">
              <h4>내 상품</h4>
            </div>
            <div>
              <a>더보기</a>
            </div>
          </div>


          <div class="popular d-flex align-items-start" >
            <div class="no-story" v-if="noStory">
              <p>아직 등록한 상품이 없어요.😅</p>
            </div>
            <div v-for="story in storyStore.recent6List" :key="story" v-else>
              <div class="popular-pic mx-2">
                <img class="pic" :src="`src/assets/image/story/${story.image}`" alt="이미지">
              </div>
            </div>
          </div>
        </div>
      </div>
    </div> -->


  </div>
</template>

<script setup>
import { useStoryStore } from '@/stores/story';
import { useUserStore } from '@/stores/user';
import { useProductStore } from '@/stores/product'
import { computed, onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const productStore = useProductStore()
const userStore = useUserStore()
const storyStore = useStoryStore()
const router = useRouter()

onMounted(() => {
  userStore.getUser();
  userStore.getFollowing(sessionStorage.getItem("loginUser"));
  userStore.getFollower(sessionStorage.getItem("loginUser"));
  storyStore.getUserStory();
  userStore.getProductScrap(sessionStorage.getItem("loginUser"));
  userStore.getStoryScrap(sessionStorage.getItem("loginUser"));
});
const productAllImages = computed(()=>{
  return productStore.productAllImages
})


const goSetting = function () {
  router.push({ name: 'setting' })
}

const goSellerRegist = function () {
  router.push({ name: 'seller-regist' })
}

const goProductRegist = function () {
  router.push({ name: 'productRegist' })
}

const isUser = computed(() => {
  return sessionStorage.getItem("role") == "u" ? true : false
})

const isSeller = computed(() => {
  return sessionStorage.getItem("role") == "s" ? true : false
})

const noStory = computed(() => {
  return storyScrapList.value.length == 0 ? true : false
})

const productScrapList = computed(()=>{
  return userStore.productScrapList
})

const storyDetail = function (storyId) {
  router.push({ name: 'storyDetail', params: { 'storyId': storyId } })
}

const storyScrapList = computed(()=>{
  return userStore.storyScrapList
})

const storyScrapList6 = computed(()=>{
  return storyScrapList.value.slice(0,6)
})
</script>

<style scoped>
.no-story {
  width: 100%;
  height: 400px;
  border: 2px dashed rgb(153, 153, 153);
  color: rgb(153, 153, 153);
  display: flex;
  justify-content: center;
  align-items: center;
}

.seller-regist {
  width: 250px;
}

.top {
  padding-left: 80px;
  padding-right: 80px;

}

.mystory {
  position: relative;
  width: 65%;
}

.container {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  padding-left: 80px;
  padding-right: 80px;
}

.section {
  margin-bottom: 20px;
  margin-left: 80px;
  width: 100%;

}

.section-title {
  width: 100%;
  height: 50px;
  display: flex;
  margin-top: 30px;
  margin-bottom: 30px;
  justify-content: space-between;
  align-items: center;
}

.section-title a {
  font-size: 18px;
  font-weight: bold;
  color: #34C5F0;
}

.small-title>h4 {
  font-weight: bold;
}

.popular {
  display: flex;
  flex-wrap: wrap;
  width: 110%;
}

.popular-pic {
  width: 200px;
  height: 200px;
  border-radius: 8px;
  object-fit: cover;
  object-fit: cover;
  overflow: hidden;
  margin-left: 30px;
  margin-bottom: 30px;
}

.popular-pic>.pic {
  border-radius: 8px;
  object-fit: cover;
  width: 100%;
  height: 100%;
  transition: all 0.2s linear;
}

.popular-pic:hover .pic {
  width: 100%;
  height: 100%;
  transform: scale(1.1);
  cursor: pointer;
}


.profile-image {
  width: 150px;
  height: 150px;
  border-radius: 70%;
  padding: 0;
  margin-bottom: 10px;
}

.profile-nickname {
  font-size: x-large;
  font-weight: bold;
  margin-bottom: 10px;
}

.profile-box {
  margin-top: 50px;
  margin-bottom: 20px;
  border: 1px solid rgb(212, 212, 212);
  border-radius: 5px;
  padding: 20px;
  width: 250px;
  height: 500px;
  font-size: 14px;
  color: rgb(96, 96, 96)
}

.profile-box-item {
  margin: 5px 0;
}

.icon-group {
  width: 100px;

}


.under-box {
  /* margin-top: 10px; */
  margin-bottom: 30px;
}


hr {
  width: 100%;
  /* color: rgb(212, 212, 212); */
}

.unline {
  text-decoration: none;
  color: black;
}

.unline:hover {
  color: #34C5F0;
}

.icon {
  font-size: 27px;
}
</style>