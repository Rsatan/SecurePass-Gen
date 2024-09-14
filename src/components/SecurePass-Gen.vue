<template>
  <div class="securepass-gen">
    <header>
      <h1><span class="first-letter">S</span>ecurePass Gen</h1>
      <button @click="handleRefreshClick" class="refresh-button" :class="{ rotating: rotating }">
        <i class="fas fa-sync-alt"></i>
      </button>
    </header>
    <div class="settings">
      <label>Length</label>
      <span class="length">{{ length }}</span>
      <input type="range" v-model="length" min="1" max="100" />
    </div>
    <div class="options">
      <label>
        <input type="checkbox" v-model="includeUppercase" /> Uppercase
      </label>
      <label>
        <input type="checkbox" v-model="includeLowercase" /> Lowercase
      </label>
      <label>
        <input type="checkbox" v-model="includeNumbers" /> Numbers
      </label>
      <label>
        <input type="checkbox" v-model="includeSymbols" /> Symbols
      </label>
    </div>
    <div class="password-output">
      <input type="text" v-model="password" readonly />
      <button @click="copyToClipboard" :class="{ copied }">
        <i class="fas" :class="[copied ? 'fa-check' : 'fa-clipboard']"></i>
      </button>
    </div>
    <footer>
      <p><a href="https://github.com/rsatan/securepass-gen">Source</a>｜<a href="https://blog.rsa7an.top">Made by Rsa7an</a></p>
    </footer>
  </div>
</template>
<script>
export default {
  data() {
    return {
      length: 16,
      includeUppercase: true,
      includeLowercase: true,
      includeNumbers: true,
      includeSymbols: false,
      password: '',
      copied: false,
      rotating: false // Use rotating instead of loading for button rotation
    };
  },
  mounted() {
    // Generate password on page load
    this.generatePassword();
  },
  watch: {
    // Watch for changes and regenerate password
    length: 'generatePassword',
    includeUppercase: 'generatePassword',
    includeLowercase: 'generatePassword',
    includeNumbers: 'generatePassword',
    includeSymbols: 'generatePassword'
  },
  methods: {
    handleRefreshClick() {
      if (!this.rotating) {
        this.rotating = true;
        this.generatePassword().finally(() => {
          setTimeout(() => {
            this.rotating = false; // Reset rotating after the animation duration
          }, 1000); // Ensure a 1-second delay for the animation to complete
        });
      }
    },
    async generatePassword() {
      const passwordChars = [];
      const allChars = {
        uppercase: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
        lowercase: 'abcdefghijklmnopqrstuvwxyz',
        numbers: '0123456789',
        symbols: '!@#$%^&*()_+[]{}|;:,.<>?'
      };

      let availableChars = '';
      if (this.includeUppercase) passwordChars.push(...allChars.uppercase);
      if (this.includeLowercase) passwordChars.push(...allChars.lowercase);
      if (this.includeNumbers) passwordChars.push(...allChars.numbers);
      if (this.includeSymbols) passwordChars.push(...allChars.symbols);


      if (availableChars.length === 0) {
        this.password = '';
      } 
      const crypto = window.crypto || window.msCrypto;
      const randomValues = new Uint32Array(this.length);
      crypto.getRandomValues(randomValues);
      let password = '';
      for (let i = 0; i < this.length; i++) {
        const randomIndex = randomValues[i] % passwordChars.length;
        password += passwordChars[randomIndex];
      }
      this.password = password;
    },
    copyToClipboard() {
      navigator.clipboard.writeText(this.password).then(() => {
        this.copied = true;
        setTimeout(() => {
          this.copied = false;
        }, 1000);
      });
    }
  }
};
</script>

<style scoped>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css');
.securepass-gen {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
  background: #1d1d1d;
  border-radius: 10px;
  max-width: 500px;
  margin: 0 auto;
  font-family: 'Roboto', sans-serif;
  color: #fff;
}

header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 1.5rem;
}

h1 {
  font-size: 2rem;
  font-weight: bold;
}

.first-letter {
  font-size: 4rem;
}

.length {
  font-size: 1.5rem;
  margin-left: 0.5rem;
}

.refresh-button {
  display: inline-block;
  text-decoration: none;
  font-size: 2.5rem;
  background: none;
  border: none;
  color: #fff;
  cursor: pointer;
  transition: color 0.3s;
  user-select: none;
}

.refresh-button:hover {
  color: #ccc;
}

.refresh-button:focus {
  outline: none;
}

.refresh-button.rotating i {
  animation: rotate 0.5s linear; 
}

@keyframes rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(180deg);
  }
}

.settings {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 1.5rem;
  border-bottom: 1px solid #555;
  padding-bottom: 1rem;
}

.settings label {
  font-size: 1rem;
  color: #ccc;
}

.settings input[type="range"] {
  flex: 1;
  margin-left: 0.5rem;
}

.options {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 1.5rem;
}

.options label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: #ccc;
}

.password-output {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  width: 100%;
}

.password-output input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  font-size: 1.2rem;
  border: 1px solid #333;
  border-radius: 5px;
  background-color: #333;
  color: #fff;
  text-align: center;
}

.password-output button {
  font-size: 1.5rem;
  background: none;
  border: none;
  color: #fff;
  cursor: pointer;
  transition: color 0.3s;
}

.password-output button:hover {
  color: #ccc;
}

button.copied {
  color: #aaa;
}
footer {
  margin-top: 1rem; 
  text-align: center; 
  font-size: 0.8rem;
  color: #908f8f;
}
footer a {
  color: inherit;
  text-decoration: none;
}

footer a:hover {
  text-decoration: underline;
}

@media (max-width: 600px) {
  .secure-pass {
    padding: 1rem;
    border-radius: 5px;
  }

  h1 {
    font-size: 1.5rem;
  }

  .refresh-button {
    font-size: 1.2rem;
  }

  .settings,
  .options,
  .password-output {
    flex-direction: column;
    align-items: stretch;
    gap: 1rem;
  }

  .options label {
    width: 100%;
  }

  .settings input[type="range"] {
    margin-left: 0;
  }

  .password-output input[type="text"] {
    font-size: 1rem;
    padding: 0.4rem;
  }

  .password-output button {
    font-size: 1.2rem;
  }
}
</style>
