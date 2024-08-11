<template>
  <div id="app">
    <h1>Dynamic Image Gallery</h1>
    <input type="file" @change="onFileChange" multiple />
    <button @click="uploadImages">Upload</button>
    
    <div v-if="uploading" class="upload-progress-container">
      <label for="uploadProgress">Upload Progress:</label>
      <input 
        type="range" 
        id="uploadProgress" 
        min="0" 
        max="100" 
        :value="uploadProgress" 
        disabled 
      />
      <span>{{ uploadProgress }}%</span>
    </div>
    
    <div class="gallery">
      <div v-for="(image, index) in images" :key="index" class="image-item">
        <img 
          :src="image.url" 
          :alt="'Image ' + index" 
          class="image-preview" 
          @click="showImage(index)" />
        <button class="delete-btn" @click="deleteImage(index)">✖</button>
        <div class="image-name">{{ image.name }}</div>
      </div>
    </div>

    <div v-if="showModal" class="modal" @click.self="closeModal">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <button class="nav-button first" @click="showFirstImage">&lt;&lt;</button>
        <button class="nav-button prev" @click="showPreviousImage">&lt;</button>
        <img :src="selectedImage.url" class="modal-image" />
        <button class="nav-button next" @click="showNextImage">&gt;</button>
        <button class="nav-button last" @click="showLastImage">&gt;&gt;</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      images: [
     
    ],
      selectedFiles: [],
      uploadProgress: 0,
      uploading: false,
      showModal: false,
      selectedImageIndex: null
    };
  },
  computed: {
    selectedImage() {
      return this.images[this.selectedImageIndex] || {};
    }
  },
  methods: {
    onFileChange(event) {
      this.selectedFiles = Array.from(event.target.files);
    },
    async uploadImages() {
      if (this.selectedFiles.length === 0) {
        alert('No files selected!');
        return;
      }
      
      this.uploading = true;
      this.uploadProgress = 0;

      for (let i = 0; i < this.selectedFiles.length; i++) {
        const file = this.selectedFiles[i];
        
        // Simulate an upload process with setTimeout
        await new Promise((resolve) => {
          let progress = 0;
          const interval = setInterval(() => {
            progress += 10;
            this.uploadProgress = Math.min(progress, 100); // Ensure progress does not exceed 100%
            if (progress >= 100) {
              clearInterval(interval);
              resolve();
            }
          }, 200);
        });

        // Create a URL for the image
        const imageUrl = URL.createObjectURL(file);
        this.images.push({ url: imageUrl, name: file.name });
      }
      
      this.uploading = false;
      this.uploadProgress = 0;
      this.selectedFiles = [];
    },
    deleteImage(index) {
      URL.revokeObjectURL(this.images[index].url); // Clean up memory
      this.images.splice(index, 1);
      if (this.selectedImageIndex !== null && index <= this.selectedImageIndex) {
        this.selectedImageIndex = Math.max(0, this.selectedImageIndex - 1);
      }
    },
    showImage(index) {
      this.selectedImageIndex = index;
      this.showModal = true;
    },
    closeModal() {
      this.showModal = false;
      this.selectedImageIndex = null;
    },
    showPreviousImage() {
      if (this.selectedImageIndex > 0) {
        this.selectedImageIndex--;
      }
    },
    showNextImage() {
      if (this.selectedImageIndex < this.images.length - 1) {
        this.selectedImageIndex++;
      }
    },
    showFirstImage() {
      if (this.images.length > 0) {
        this.selectedImageIndex = 0;
      }
    },
    showLastImage() {
      if (this.images.length > 0) {
        this.selectedImageIndex = this.images.length - 1;
      }
    }
  }
};
</script>
<style scoped>
#app {
  text-align: center;
  margin-top: 20px;
}

input[type="file"] {
  margin-bottom: 10px;
}

.upload-progress-container {
  margin: 10px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.upload-progress-container label {
  margin-bottom: 5px;
  font-size: 16px;
}

input[type="range"] {
  width: 300px;
  -webkit-appearance: none;
  background: #ddd;
  border-radius: 5px;
  height: 10px;
  outline: none;
  margin-bottom: 5px;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  background: #007bff;
  border-radius: 50%;
  cursor: pointer;
}

input[type="range"]::-moz-range-thumb {
  width: 20px;
  height: 20px;
  background: #007bff;
  border-radius: 50%;
  cursor: pointer;
}

.gallery {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
}

.image-item {
  position: relative;
  border: 1px solid #ccc;
  border-radius: 5px;
  overflow: hidden;
  text-align: center;
}

.image-preview {
  max-width: 150px;
  max-height: 150px;
  border-radius: 5px;
  cursor: pointer;
}

.delete-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  background-color: rgba(255, 0, 0, 0.7);
  color: white;
  border: none;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background-color 0.3s;
}

.delete-btn:hover {
  background-color: rgba(255, 0, 0, 0.9);
}

.image-name {
  margin-top: 5px;
  font-size: 14px;
  color: #333;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px 15px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background-color: #0056b3;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  position: relative;
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-image {
  max-width: 100%;
  max-height: 80vh;
  border-radius: 8px;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 24px;
  cursor: pointer;
}

.nav-button {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 20px;
}

.nav-button:hover {
  background-color: rgba(0, 0, 0, 0.8);
}

.first {
  left: 10px;
}

.prev {
  left: 50px;
}

.next {
  right: 50px;
}

.last {
  right: 10px;
}
</style>
