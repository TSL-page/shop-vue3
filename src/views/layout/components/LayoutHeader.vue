<script setup>
import { useCategoryStore } from '@/stores/categoryStore';
import HeaderCart from './HeaderCart.vue';

const categoryStore = useCategoryStore()


import { ref } from 'vue';
import axios from 'axios';


// DeepSeekAPI
const apiKey = import.meta.env.VITE_DEEPSEEK_API_KEY;
const baseURL = import.meta.env.VITE_DEEPSEEK_BASE_URL;

// 搜索相关变量
const query = ref(''); // 搜索输入
const searchResults = ref(''); // 搜索结果
const isLoading = ref(false); // 加载状态

// 调用 DeepSeek API 进行搜索
const search = async () => {
  if (!query.value.trim()) return;

  try {
    isLoading.value = true;
    const response = await axios.post(
      `${baseURL}/chat/completions`,
      {
        model: "deepseek-chat",
        messages: [{ role: "user", content: query.value }]
      },
      {
        headers: {
          "Authorization": `Bearer ${apiKey}`,
          "Content-Type": "application/json"
        }
      }
    );
    searchResults.value = response.data.choices[0]?.message?.content || "未找到结果";
  } catch (error) {
    console.error("搜索失败:", error);
    searchResults.value = "搜索失败，请稍后再试";
  } finally {
    isLoading.value = false;
  }
}
</script>

<template>
  <header class="app-header">
    <div class="container">
      <h1 class="logo">
        <RouterLink to="/">乐购优品</RouterLink>
      </h1>
      <ul class="app-header-nav">
        <li class="home" v-for="item in categoryStore.categoryList" :key="item.id">
          <RouterLink active-class="active" :to="`/category/${item.id}`">{{ item.name }}</RouterLink>
        </li>
      </ul>
      <div class="search">
        <i class="iconfont icon-search"></i>
        <input
          type="text"
          placeholder="搜一搜"
          v-model="query"
          @keyup.enter="search"
        />
      </div>
      <!-- 显示搜索结果 -->
      <div v-if="searchResults" class="search-results">
        <p v-if="isLoading">搜索中...</p>
        <p v-else>{{ searchResults }}</p>
      </div>
      <HeaderCart />
    </div>
  </header>
</template>


<style scoped lang='scss'>
.app-header {
  background: #fff;

  .container {
    display: flex;
    align-items: center;
  }

  .logo {
    width: 200px;

    a {
      display: block;
      height: 132px;
      width: 100%;
      text-indent: -9999px;
      background: url('@/assets/images/logo.png') no-repeat center 18px / contain;
    }
  }

  .app-header-nav {
    width: 820px;
    display: flex;
    padding-left: 40px;
    position: relative;
    z-index: 998;

    li {
      margin-right: 40px;
      width: 38px;
      text-align: center;

      a {
        font-size: 16px;
        line-height: 32px;
        height: 32px;
        display: inline-block;

        &:hover {
          color: $xtxColor;
          border-bottom: 1px solid $xtxColor;
        }
      }

      .active {
        color: $xtxColor;
        border-bottom: 1px solid $xtxColor;
      }
    }
  }

  .search {
    width: 170px;
    height: 32px;
    position: relative;
    border-bottom: 1px solid #e7e7e7;
    line-height: 32px;

    .icon-search {
      font-size: 18px;
      margin-left: 5px;
    }

    input {
      width: 140px;
      padding-left: 5px;
      color: #666;
    }
  }

  .cart {
    width: 50px;

    .curr {
      height: 32px;
      line-height: 32px;
      text-align: center;
      position: relative;
      display: block;

      .icon-cart {
        font-size: 22px;
      }

      em {
        font-style: normal;
        position: absolute;
        right: 0;
        top: 0;
        padding: 1px 6px;
        line-height: 1;
        background: $helpColor;
        color: #fff;
        font-size: 12px;
        border-radius: 10px;
        font-family: Arial;
      }
    }
  }
}
</style>