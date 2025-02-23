<script setup>
import { ref } from "vue";
import axios from "axios";

const file = ref(null);
const processing = ref(false);
const errorMessage = ref("");
const previewUrl = ref(""); // 미리보기 URL 저장
const fileBlob = ref(null); // 다운로드할 Blob 저장
const isImage = ref(false); // 파일이 이미지인지 여부

const handleFileChange = (event) => {
  file.value = event.target.files[0];
  previewUrl.value = "";
  isImage.value = false;
};

const uploadFile = async () => {
  if (!file.value) {
    alert("파일을 선택하세요.");
    return;
  }

  processing.value = true;
  errorMessage.value = "";
  previewUrl.value = "";
  fileBlob.value = null;

  try {
    const formData = new FormData();
    formData.append("file", file.value);

    // 서버 API 호출 (백엔드 URL 변경 가능)
    const response = await axios.post("http://localhost:8080/upload", formData, {
      headers: { "Content-Type": "multipart/form-data" },
      responseType: "blob", // 바이너리 데이터 응답 처리
    });

    // Blob 생성
    const mimeType = response.headers["content-type"] || "application/octet-stream";
    fileBlob.value = new Blob([response.data], { type: mimeType });

    // 이미지 파일이면 미리보기 URL 생성
    if (mimeType.startsWith("image/")) {
      isImage.value = true;
      previewUrl.value = URL.createObjectURL(fileBlob.value);
    } else {
      isImage.value = false;
    }
  } catch (error) {
    console.error("업로드 실패:", error);
    errorMessage.value = "파일 업로드 중 오류 발생!";
  } finally {
    processing.value = false;
  }
};

// 파일 다운로드 기능
const downloadFile = () => {
  if (!fileBlob.value) return;

  const link = document.createElement("a");
  link.href = URL.createObjectURL(fileBlob.value);
  link.setAttribute("download", file.value.name || "downloaded_file");
  document.body.appendChild(link);
  link.click();
  link.remove();
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

    <!-- 미리보기 및 다운로드 -->
    <div v-if="fileBlob" class="response-box">
      <h3>📌 서버 응답 데이터:</h3>
      <img v-if="isImage" :src="previewUrl" alt="업로드된 이미지" style="max-width: 300px; margin-top: 10px;" />
      <button @click="downloadFile">다운로드</button>
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
  color: black;
}
</style>
