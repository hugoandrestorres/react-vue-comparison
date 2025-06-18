<template>
  <div class="dog-slider">
    <h2>Random Dog Slider</h2>
    
    <div v-if="loading" class="loading">
      Loading dogs...
    </div>
    
    <div v-else-if="error" class="error">
      Error loading dogs: {{ error }}
    </div>
    
    <div v-else class="slider-container">
      <div class="slider-wrapper">
        <div 
          class="slider-track" 
          :style="{ transform: `translateX(-${currentIndex * 100}%)` }"
        >
          <div 
            v-for="(dog, index) in dogs" 
            :key="index" 
            class="slide"
          >
            <img 
              :src="dog.url" 
              :alt="`Dog ${index + 1}`"
              @load="onImageLoad"
              @error="onImageError"
            />
            <p class="dog-info">Dog #{{ index + 1 }}</p>
          </div>
        </div>
      </div>
      
      <div class="controls">
        <button 
          @click="previousSlide" 
          :disabled="currentIndex === 0"
          class="nav-button prev"
        >
          ← Previous
        </button>
        
        <div class="indicators">
          <span 
            v-for="(dog, index) in dogs" 
            :key="index"
            @click="goToSlide(index)"
            :class="['indicator', { active: index === currentIndex }]"
          ></span>
        </div>
        
        <button 
          @click="nextSlide" 
          :disabled="currentIndex === dogs.length - 1"
          class="nav-button next"
        >
          Next →
        </button>
      </div>
      
      <button @click="loadMoreDogs" class="load-more">
        Load More Dogs
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DogSlider',
  data() {
    return {
      dogs: [],
      currentIndex: 0,
      loading: true,
      error: null
    }
  },
  async mounted() {
    await this.loadDogs()
  },
  methods: {
    async fetchDogImage() {
      try {
        const response = await fetch('https://random.dog/woof.json')
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }
        const data = await response.json()
        return data
      } catch (error) {
        console.error('Error fetching dog image:', error)
        throw error
      }
    },
    
    async loadDogs(count = 5) {
      this.loading = true
      this.error = null
      
      try {
        const dogPromises = Array(count).fill().map(() => this.fetchDogImage())
        const newDogs = await Promise.all(dogPromises)
        
        // Filter out videos and only keep images
        const imageDogs = newDogs.filter(dog => 
          dog.url && (dog.url.endsWith('.jpg') || dog.url.endsWith('.jpeg') || dog.url.endsWith('.png') || dog.url.endsWith('.gif'))
        )
        
        this.dogs = [...this.dogs, ...imageDogs]
        
        if (this.dogs.length === 0) {
          throw new Error('No valid dog images found')
        }
      } catch (error) {
        this.error = error.message
      } finally {
        this.loading = false
      }
    },
    
    async loadMoreDogs() {
      await this.loadDogs(3)
    },
    
    nextSlide() {
      if (this.currentIndex < this.dogs.length - 1) {
        this.currentIndex++
      }
    },
    
    previousSlide() {
      if (this.currentIndex > 0) {
        this.currentIndex--
      }
    },
    
    goToSlide(index) {
      this.currentIndex = index
    },
    
    onImageLoad() {
      // Image loaded successfully
    },
    
    onImageError(event) {
      console.error('Failed to load image:', event.target.src)
    }
  }
}
</script>

<style scoped>
.dog-slider {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h2 {
  color: #42b883;
  margin-bottom: 20px;
}

.loading, .error {
  padding: 20px;
  text-align: center;
  font-size: 18px;
}

.error {
  color: #e74c3c;
  background-color: #fdf2f2;
  border: 1px solid #e74c3c;
  border-radius: 4px;
}

.slider-container {
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.slider-wrapper {
  position: relative;
  width: 100%;
  height: 400px;
  overflow: hidden;
}

.slider-track {
  display: flex;
  height: 100%;
  transition: transform 0.3s ease-in-out;
}

.slide {
  min-width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
}

.slide img {
  max-width: 100%;
  max-height: 350px;
  object-fit: contain;
  border-radius: 8px;
}

.dog-info {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px 15px;
  border-radius: 20px;
  margin: 0;
  font-size: 14px;
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  background: #f8f9fa;
}

.nav-button {
  background: #42b883;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.2s;
}

.nav-button:hover:not(:disabled) {
  background: #369870;
}

.nav-button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.indicators {
  display: flex;
  gap: 8px;
}

.indicator {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: #ddd;
  cursor: pointer;
  transition: background-color 0.2s;
}

.indicator.active {
  background: #42b883;
}

.indicator:hover {
  background: #369870;
}

.load-more {
  width: 100%;
  background: #3498db;
  color: white;
  border: none;
  padding: 15px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.load-more:hover {
  background: #2980b9;
}

@media (max-width: 768px) {
  .controls {
    flex-direction: column;
    gap: 15px;
  }
  
  .nav-button {
    padding: 8px 16px;
    font-size: 12px;
  }
}
</style>

