<template>
  <div class="hello">
    <h1>Drag and Drop</h1>
    <form @submit.prevent="submit">
      <div class="form-container">
        <!-- Left Section: Drag and Drop File Area -->
        <div
          class="file-drop-area"
          @dragover.prevent="isDragOver = true"
          @dragleave.prevent="isDragOver = false"
          @drop.prevent="handleFileDrop"
          @click="triggerFileInput"
          :class="{ 'is-dragover': isDragOver }"
        >
          <p v-if="!file">Drag & drop or click to upload</p>
          <div v-else class="file-info">
            <span class="file-name">{{ file.name }}</span>
            <span class="remove-icon" @click.stop="removeFile">×</span>
          </div>
          <input
            type="file"
            id="file"
            @change="handleFileChange"
            ref="fileInput"
            style="display: none"
          />
        </div>

        <!-- Right Section: Form Fields -->
        <div class="form-fields">
          <div class="form-group">
            <label for="name"
              >Name<span
                style="color: black; font-size: 16px; font-weight: bold"
                >*</span
              ></label
            >
            <input
              type="text"
              id="name"
              placeholder="Enter your Name"
              v-model="form.name"
            />
            <p v-if="errors.name" class="error">{{ errors.name }}</p>
          </div>
          <div class="form-group">
            <label for="message">Message</label>
            <input
              type="text"
              id="message"
              placeholder="Enter your Message"
              v-model="form.message"
            />
          </div>

          <!-- Date and Time of Upload -->
          <div class="form-group">
            <label for="upload-time">Upload Time</label>
            <input
              type="text"
              id="upload-time"
              v-model="form.uploadTime"
              readonly
              disabled
              placeholder="Upload time will be shown here"
            />
          </div>
        </div>
      </div>

      <!-- Submit Button -->
      <div class="submit-btn">
        <button type="submit" :disabled="isProcessing">Submit</button>
      </div>
    </form>

    <!-- Loader Overlay -->
    <div v-if="isProcessing" class="loader-overlay">
      <div class="loader"></div>
      <p>Processing...</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomePage",
  data() {
    return {
      form: {
        name: "",
        message: "",
        uploadTime: "", // Holds the upload time
      },
      file: null,
      isDragOver: false,
      errors: {
        name: null,
      },
      isProcessing: false, // Loader state
    };
  },
  methods: {
    validateForm() {
      let isValid = true;

      // Validate Name
      if (!this.form.name.trim()) {
        this.errors.name = "Name is required.";
        isValid = false;
      } else {
        this.errors.name = null;
      }

      return isValid;
    },
    async submit() {
      if (this.validateForm()) {
        this.isProcessing = true; // Show loader
        console.warn("Submitted Data:", this.form);
        console.warn("Submitted File:", this.file);

        // Simulate a server request
        setTimeout(() => {
          this.isProcessing = false; // Hide loader
          alert("Form submitted successfully!");
        }, 2000); // 2 seconds for demo
      } else {
        console.warn("Form validation failed.");
      }
    },
    handleFileDrop(event) {
      const droppedFile = event.dataTransfer.files[0];
      if (droppedFile) {
        this.file = droppedFile;
        this.setUploadTime(); // Set the upload time when file is dropped
      }
      this.isDragOver = false;
    },
    handleFileChange(event) {
      const selectedFile = event.target.files[0];
      if (selectedFile) {
        this.file = selectedFile;
        this.setUploadTime(); // Set the upload time when file is selected
      }
    },
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    removeFile() {
      this.file = null;
      this.form.uploadTime = ""; // Clear the upload time if the file is removed
    },
    setUploadTime() {
      const currentDate = new Date();
      this.form.uploadTime = currentDate.toLocaleString(); // Set the current date and time
    },
  },
};
</script>

<style scoped>
/* General Styles */
h1 {
  color: #42b983;
  text-align: center;
}
form {
  max-width: 800px;
  margin: 30px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background: #f9f9f9;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

/* Layout: Two Sections */
.form-container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 20px;
  padding: 10px;
}
.file-drop-area,
.form-fields {
  flex: 1;
}

/* Drag and Drop Area */
.file-drop-area {
  height: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px;
  border: 2px dashed #ccc;
  text-align: center;
  cursor: pointer;
  border-radius: 8px;
  background: #f3f4f6;
  transition: background-color 0.3s, border-color 0.3s;
  position: relative;
}
.file-drop-area.is-dragover {
  background-color: #e6f7ff;
  border-color: #69c0ff;
}
.file-info {
  position: relative;
  width: 100%;
  text-align: left;
  padding: 10px;
  background-color: #ffffff;
  border: 1px solid #ddd;
  border-radius: 5px;
}
.remove-icon {
  position: absolute;
  top: 5px;
  right: 10px;
  font-size: 16px;
  font-weight: bold;
  color: #f56565;
  cursor: pointer;
}
.remove-icon:hover {
  color: #e53e3e;
}

/* Form Fields */
.form-group {
  display: flex;
  flex-direction: column;
}
label {
  font-weight: bold;
  margin-bottom: 5px;
}
input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 14px;
}

/* Loader Overlay */
.loader-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(255, 255, 255, 0.8);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.loader {
  border: 5px solid #ccc;
  border-top: 5px solid #42b983;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}
.submit-btn {
  display: flex;
  justify-content: center;
  margin-top: 20px; /* Space between the form and the button */
}

button {
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #369973;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
