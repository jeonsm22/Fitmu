<template>
    <div class="container">
        <div class="pic-div">
            <img class="main-pic" :src="`/src/assets/image/product/${productStore.productImages[0].fileName}`" alt="이미지">
        </div>
        <div class="info">
            <p class="brand">{{ productStore.product.brand }}</p>
            <div class="namediv">
                <h3 class="name">{{ productStore.product.name }}</h3>
                <i v-if="isLike(route.params.productId)" class="bi bi-heart-fill heart setRed" @click = "YesProduct(route.params.productId)"></i>
                <i v-else @click = "YesProduct(route.params.productId)" class="heart bi bi-heart"></i>

            </div>
            ⭐<span class="rating" v-if="productStore.reviews.length != 0">{{ rating }}</span>
            <span class="rating" v-else>0</span>
            <span>{{ productStore.reviews.length}}개 리뷰</span>
            <br>
            <span class="discount">{{ productStore.product.discountRate }}%</span>
            <span class="price">{{ productStore.product.price.toLocaleString('ko-KR') }}원</span><br>
            <div class="price2">
                <p class="specialPrice">{{ productStore.product.specialPrice.toLocaleString('ko-KR') }}</p>
                <p>원</p>
                <p class="xmrrk">특가</p>
            </div>
            <div class="delivery">
                <span class="delivery2">배송비</span>
                <span class="deliveryFee" v-if="product.deliveryFee != 0">{{productStore.product.deliveryFee.toLocaleString('ko-KR') }}원</span>
                <span class="deliveryFee" v-else>무료배송</span>
            </div>
            <hr>
            <div class = "btndiv">
                <label for="quantity">수량</label>
                <select name="quantity" id="quantity" v-model = "quantity">
                    <option value="1" selected>1</option>
                    <option value="2">2</option>
                    <option value="3">3</option>
                    <option value="4">4</option>
                    <option value="5">5</option>
                    <option value="6">6</option>
                    <option value="7">7</option>
                    <option value="8">8</option>
                    <option value="9">9</option>
                    <option value="10">10</option>
                </select>
                <span class="max-quantity">최대 주문 가능 수량 : 10개</span>
                <div class = "totalPrice">
                    <p>주문금액</p>
                    <p>{{ (productStore.product.specialPrice * quantity).toLocaleString('ko-KR') }}원</p>
                </div>
                <button class = "buybtn" @click="goOrder">바로 구매</button>
            </div>
        </div>
    </div>
    <div class="list">
        <div class="router">
            <RouterLink :to="{ name: 'productDetail', params: { 'productId': productId } }">상품정보</RouterLink>
            <RouterLink :to="{ name: 'productreview', params: { 'productId': productId } }">리뷰</RouterLink>
            <RouterLink :to="{ name: 'productinquiry', params: { 'productId': productId } }">문의</RouterLink>
        </div>
    </div>
    <div>
        <RouterView />
    </div>
</template>

<script setup>
import { onMounted, ref, computed, onUpdated, onBeforeMount } from 'vue';
import { useRoute, useRouter } from 'vue-router'
import { useProductStore } from '@/stores/product'

const route = useRoute()
const router = useRouter()
const productStore = useProductStore()

productStore.getProduct(route.params.productId)
productStore.getProductImages(route.params.productId)
productStore.getProductReviews(route.params.productId)
productStore.getUsers()
productStore.getProductInquiry(route.params.productId)
productStore.getProductLike()
// onBeforeMount(()=>{
// })

const productId = ref(route.params.productId)
const product = ref(productStore.product)
const productImages = computed(()=>{
        return productStore.productImages

})
const reviews = ref(productStore.reviews)
const mainImage = ref(productImages.value[0].fileName)

const productLike = computed(()=>{
    return productStore.productLike
})

const quantity = ref(1)

const YesProduct = function(id){
    if(isLike(id)){
        let index = productLike.value.findIndex((product)=> product.storyId == id)
        productLike.value.splice(index, 1)
        productStore.NoProduct(id)
    }else{
        productLike.value.push(productStore.product)
        productStore.YesProduct(id)
    }
}

const isLike = function(id){
    for(let i=0; i<productLike.value.length; i++){
        if(productLike.value[i].productId == id){
            return true
        }
    }
    return false
}


const rating = computed(()=>{
    let sum = 0
    for(let i=0; i<productStore.reviews.length; i++){
        sum += productStore.reviews[i].rating
    }
    return (sum / productStore.reviews.length).toFixed(1)
})

const goOrder = function(){
    if(quantity.value == 0){
        window.alert("수량을 선택해주세요")
        return
    }
    router.replace({ name: 'order', params: { 'productId': route.params.productId, 'quantity': quantity.value } })
}
</script>

<style scoped>
.setRed{
    color : red;
}
.totalPrice{
    display : flex;
    justify-content : space-between;
    margin-top : 68px
}
#quantity{
    height : 35px;
    padding-left : 15px;
    border : 1px solid #34C5F0;
    border-radius : 5px;
}

.buybtn{
    margin-top : 10px;
    height : 40px;
    border : 1px solid #34C5F0;
    background-color: #34C5F0;
    color : white;
    font-weight: bold;
    border-radius: 5px;
}

.buybtn:hover{
    border : 1px solid #2ca0c4;
    background-color: #2ca0c4;
}
.btndiv{
    display : flex;
    flex-direction: column;
    padding-top : 20px;
    font-weight: bold;
}
.btndiv label{
    margin-bottom: 5px;
}
.router {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 40px;
}

.router a {
    text-decoration: none;
    font-weight: bold;
    margin-left: 50px;
    margin-right: 50px;
    color: black;
}

.router a:hover {
    color: #34C5F0
}

hr {
    color: #EDEDED;
}

.list {
    background-color: #FAFAFA;
    border: 2px solid #EDEDED;
}

.heart {
    font-size: 30px;
    margin-right: 30px;
}
.heart:hover {
    font-size: 30px;
    margin-right: 30px;
    color : red;
    cursor : pointer;
}

.rating {
    margin-right: 10px;
    margin-left: 5px;
}

.xmrrk {
    color: white;
    font-size: 12px;
    background-color: #FF7777;
    padding: 5px 7px 5px 5px;
    border-radius: 10px;
    font-weight: bold;
    margin-left: 6px;
}

.price2 {
    display: flex;
    font-size: 25px;
    align-items: center;

}

.container {
    width: 100%;
    display: flex;
    padding-left: 80px;
    padding-right: 80px;
}

.pic-div {
    width: 600px;
    height: 600px;
    margin: 60px;
}

.main-pic {
    width: 100%;
    height: 100%;
    border-radius: 5px;
}

.info {
    width: 600px;
    margin-top: 90px;
    padding: 10px;
}

.brand {
    font-size: 15px;
    font-weight: bold;
}

.name {
    font-size: 25px;
    font-weight: bold;
    margin-bottom: 30px;
}

.namediv {
    display: flex;
    width: 100%;
    justify-content: space-between;
}

.discount {
    font-size: 20px;
    margin-right: 10px;
    font-weight: bold;
    color: #7B7977;
}

.price {
    font-size: 20px;
    text-decoration: line-through;
    text-decoration-color: #DEDEDD;
    /* 선의 색상 설정 */
    text-decoration-thickness: 2px;
    /* 선의 두께 설정 */
    color: #DEDEDD;
}

.specialPrice {
    font-size: 45px;
    font-weight: bold;
}

.delivery {
    display: flex;
    align-items: center;
}

.delivery2 {
    font-size: 17px;
    margin-left: 20px;
    margin-right: 10px;
    font-weight: bold;
    color: #7B7977;
}

.deliveryFee {
    font-size: 17px;
    margin-left: 10px;
    font-weight: bold;
}

.max-quantity{
    color: rgb(177, 177, 177);
    margin-top: 5px;
    font-size: 13px; 
    font-weight: 400;  
}
</style>