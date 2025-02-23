<script setup>
import { ref } from "vue";
import axios from "axios";

const files = ref(null);
const processing = ref(false);
const errorMessage = ref("");
const responseData = ref([]); // 서버 응답 데이터를 저장할 변수

const handleFileChange = (event) => {
  files.value = event.target.files;
};

const uploadFiles = async () => {
  if (!files.value || files.value.length === 0) {
    alert("파일을 선택하세요.");
    return;
  }

  processing.value = true;
  errorMessage.value = "";
  responseData.value = [];

  try {
    const formData = new FormData();
    for (let i = 0; i < files.value.length; i++) {
      formData.append("files", files.value[i]);
    }

    // 서버에 파일 업로드 요청
    const response = await axios.post("http://localhost:8080/uploads", formData, {
      headers: { "Content-Type": "multipart/form-data" },
    });

    // 서버에서 반환된 파일명, 파일 URL 리스트 저장
    responseData.value = response.data;
  } catch (error) {
    console.error("업로드 실패:", error);
    errorMessage.value = "파일 업로드 중 오류 발생!";
  } finally {
    processing.value = false;
  }
};

const downloadFile = (fileName) => {
  const url = `http://localhost:8080/download/${fileName}`;
  const link = document.createElement("a");
  link.href = url;
  link.setAttribute("download", fileName); // URL에서 파일명 추출
  document.body.appendChild(link);
  link.click();
  // 다운로드 후 링크 요소 제거
  document.body.removeChild(link);
};


const previewFile = (fileName) => {
  const url = `http://localhost:8080/preview/${fileName}`;
  return url
};
</script>

<template>
  <div class="upload-container">
    <h2>GPS 정보 추가 파일 업로드</h2>
    <input type="file" @change="handleFileChange" accept="image/*" multiple />
    <button @click="uploadFiles" :disabled="processing">
      {{ processing ? "처리 중..." : "업로드" }}
    </button>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

     <!-- 업로드된 파일 미리보기 및 다운로드 -->
     <div v-if="responseData.length > 0" class="response-box">
      <h3>업로드된 파일:</h3>
      <ul>
        <li v-for="(file, index) in responseData" :key="index">
          <img :src="previewFile(file.fileName)" alt="미리보기" style="max-width: 150px; margin-top: 10px;" />
          <p>{{ file.fileName }}</p>
          <button @click="downloadFile(file.fileName)">다운로드</button>
        </li>
      </ul>
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
.file-list {
  margin-top: 20px;
}
</style>
