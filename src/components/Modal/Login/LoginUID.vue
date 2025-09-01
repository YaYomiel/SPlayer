<template>  
  <div class="login-uid">  
    <n-alert type="info" style="margin-bottom: 16px;">  
      请输入网易云音乐UID进行身份验证  
    </n-alert>  
    <n-input   
      v-model:value="uid"   
      placeholder="请输入UID（纯数字）"   
      :allow-input="onlyAllowNumber"  
      :loading="loading"  
      @keydown.enter="handleLogin"  
    >  
      <template #prefix>  
        <n-icon>  
          <SvgIcon icon="user" />  
        </n-icon>  
      </template>  
    </n-input>  
    <n-flex class="menu" style="margin-top: 16px;">  
      <n-button   
        type="primary"   
        :loading="loading"  
        :disabled="!uid"  
        @click="handleLogin"  
        style="width: 100%"  
      >  
        验证登录  
      </n-button>  
    </n-flex>  
  </div>  
</template>  
  
<script setup>  
import { ref } from "vue";  
  
const emit = defineEmits(["setLoginData"]);  
  
const uid = ref("");  
const loading = ref(false);  
  
// 只允许输入数字  
const onlyAllowNumber = (value) => !value || /^\d+$/.test(value);  
  
// 模拟UID验证API调用  
const verifyUserByUID = async (uid) => {  
  // 这里应该调用实际的API，暂时模拟  
  return new Promise((resolve, reject) => {  
    setTimeout(() => {  
      if (uid && uid.length >= 6) {  
        resolve({  
          code: 200,  
          profile: {  
            userId: uid,  
            nickname: `用户${uid}`,  
            avatarUrl: "/imgs/icons/favicon.png"  
          }  
        });  
      } else {  
        reject(new Error("无效的UID"));  
      }  
    }, 1000);  
  });  
};  
  
const handleLogin = async () => {  
  if (!uid.value) {  
    $message.warning("请输入UID");  
    return;  
  }  
    
  loading.value = true;  
    
  try {  
    const result = await verifyUserByUID(uid.value);  
      
    if (result.code === 200 && result.profile) {  
      $message.success(`验证成功：${result.profile.nickname}`);  
        
      // 构造登录数据，标记为UID验证模式  
      const loginData = {  
        code: 200,  
        profile: result.profile,  
        cookie: "", // UID登录无法获得cookie  
        isUIDLogin: true, // 标记为UID登录  
      };  
        
      emit("setLoginData", loginData);  
    } else {  
      $message.error("UID不存在或无法访问");  
    }  
  } catch (error) {  
    console.error("UID验证失败：", error);  
    $message.error("验证失败，请检查UID是否正确");  
  } finally {  
    loading.value = false;  
  }  
};  
</script>  
  
<style lang="scss" scoped>  
.login-uid {  
  margin-top: 20px;  
  min-height: 120px;  
  transition: height 0.3s ease;  
    
  .menu {  
    margin-top: 16px;  
  }  
}  
</style>
