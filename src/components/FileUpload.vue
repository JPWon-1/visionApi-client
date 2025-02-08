<script setup>
import { ref } from "vue";
import axios from "axios";

const file = ref(null);
const processing = ref(false);
const errorMessage = ref("");
const responseData = ref(null); // 서버 응답 데이터를 저장할 변수

const handleFileChange = (event) => {
  file.value = event.target.files[0];
};

const uploadFile = async () => {
  if (!file.value) {
    alert("파일을 선택하세요.");
    return;
  }

  processing.value = true;
  errorMessage.value = "";
  responseData.value = null;

  try {
    const formData = new FormData();
    formData.append("file", file.value);

    // 서버 API 호출 (백엔드 URL로 변경)
    const response = await axios.post("http://localhost:8080/upload", formData, {
      headers: { "Content-Type": "multipart/form-data" },
    });

    // JSON 응답 데이터를 화면에 표시
    responseData.value = response.data;

  } catch (error) {
    console.error("업로드 실패:", error);
    errorMessage.value = "파일 업로드 중 오류 발생!";
  } finally {
    processing.value = false;
  }
};
</script>

<template>
  <div class="upload-container">
    <h2>GPS 정보 추가 파일 업로드</h2>
    <input type="file" @change="handleFileChange" accept="image/*" />
    <button @click="uploadFile" :disabled="processing">
      {{ processing ? "처리 중..." : "업로드" }}
    </button>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <!-- 서버에서 받은 JSON 데이터 출력 -->
    <div v-if="responseData" class="response-box">
      <h3>📌 서버 응답 데이터:</h3>
      <pre>{{ JSON.stringify(responseData, null, 2) }}</pre>
    </div>
  </div>
</template>

<style scoped>
.upload-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 500px;
  margin: auto;
  text-align: center;
}
button {
  padding: 10px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
}
button:disabled {
  background-color: #aaa;
}
.error {
  color: red;
}
.response-box {
  margin-top: 20px;
  padding: 10px;
  background: #f4f4f4;
  border-radius: 5px;
  text-align: left;
  word-break: break-word;
  color:black
}
pre {
  white-space: pre-wrap;
  font-size: 14px;
  text-align: left;
}
</style>
