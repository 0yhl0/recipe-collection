<template>
  <div class="app">
    <h1>🍳 食谱收藏夹</h1>
    
    <!-- 搜索栏 -->
    <SearchBar 
      :init-keyword="searchKeyword"
      @search="handleSearch"
      @reset="handleReset"
    />
    
    <!-- 视图切换：列表/详情 -->
    <div v-if="!currentRecipeId">
      <RecipeList 
        :recipes="allRecipes"
        :search-keyword="searchKeyword"
        :collected-ids="collectedIds"
        @goDetail="goToDetail"
        @toggleCollect="handleToggleCollect"
      />
    </div>
    
    <div v-else>
      <RecipeDetail 
        :recipe="currentRecipe"
        :collected-ids="collectedIds"
        @goBack="goBackToList"
        @toggleCollect="handleToggleCollect"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import SearchBar from './components/SearchBar.vue'
import RecipeList from './components/RecipeList.vue'
import RecipeDetail from './components/RecipeDetail.vue'
import recipesData from './data/recipes.json'

// 所有菜谱数据
const allRecipes = ref(recipesData)

// 搜索关键词
const searchKeyword = ref('')

// 收藏的菜谱ID (持久化到localStorage)
const collectedIds = ref(JSON.parse(localStorage.getItem('recipeCollectIds') || '[]'))

// 当前选中的菜谱ID（控制详情页显示）
const currentRecipeId = ref(null)

// 当前选中的菜谱
const currentRecipe = computed(() => {
  return allRecipes.value.find(recipe => recipe.id === currentRecipeId.value) || null
})

// 搜索处理
const handleSearch = (keyword) => {
  searchKeyword.value = keyword
}

// 重置搜索
const handleReset = () => {
  searchKeyword.value = ''
}

// 跳转到详情页
const goToDetail = (id) => {
  currentRecipeId.value = id
}

// 返回列表页
const goBackToList = () => {
  currentRecipeId.value = null
}

// 收藏/取消收藏
const handleToggleCollect = (id) => {
  if (collectedIds.value.includes(id)) {
    // 取消收藏
    collectedIds.value = collectedIds.value.filter(item => item !== id)
  } else {
    // 加入收藏
    collectedIds.value.push(id)
  }
  // 持久化到本地存储
  localStorage.setItem('recipeCollectIds', JSON.stringify(collectedIds.value))
}

// 页面加载时读取本地存储
onMounted(() => {
  const savedIds = localStorage.getItem('recipeCollectIds')
  if (savedIds) {
    collectedIds.value = JSON.parse(savedIds)
  }
})
</script>

<style scoped>
.app {
  max-width: 1200px;
  margin: 0 auto;
}
h1 {
  text-align: center;
  color: #333;
  margin: 20px 0;
}
</style>