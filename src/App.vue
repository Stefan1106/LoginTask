<template>
  <div class="login-container">
    <h2>Login</h2>
    <form @submit.prevent="handleLogin">
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" id="email" v-model="email" required />
      </div>
      <div class="form-group">
        <label for="password">Password</label>
        <input
          type="password"
          id="password"
          v-model="password"
          @input="validatePassword"
          required
        />
        <!-- Display password strength message -->
        <div v-if="passwordScoreMessage" class="score-message">
          Password strength: {{ passwordScoreMessage }}
        </div>
        <div v-if="passwordErrors.length" class="error-message">
          <ul>
            <li v-for="(error, index) in passwordErrors" :key="index">{{ error }}</li>
          </ul>
        </div>
      </div>
      <button type="submit" :disabled="!passwordValid">Login</button>
    </form>
  </div>

  <!-- Password requirements displayed horizontally below the login container -->
  <div v-if="passwordRequirements" class="requirements-container">
    <div :class="{ valid: passwordLengthValid, invalid: !passwordLengthValid }">
      <span v-if="passwordLengthValid">&#x2714;</span>
      <span v-else>&#x2718;</span> Minimum 8 characters
    </div>
    <div :class="{ valid: hasUppercase, invalid: !hasUppercase }">
      <span v-if="hasUppercase">&#x2714;</span>
      <span v-else>&#x2718;</span> Contains an uppercase letter
    </div>
    <div :class="{ valid: hasLowercase, invalid: !hasLowercase }">
      <span v-if="hasLowercase">&#x2714;</span>
      <span v-else>&#x2718;</span> Contains a lowercase letter
    </div>
    <div :class="{ valid: hasNumber, invalid: !hasNumber }">
      <span v-if="hasNumber">&#x2714;</span>
      <span v-else>&#x2718;</span> Contains a number
    </div>
    <div :class="{ valid: hasSpecial, invalid: !hasSpecial }">
      <span v-if="hasSpecial">&#x2714;</span>
      <span v-else>&#x2718;</span> Contains a special character
    </div>
    <div :class="{ valid: !hasConsecutiveLetters, invalid: hasConsecutiveLetters }">
      <span v-if="!hasConsecutiveLetters">&#x2714;</span>
      <span v-else>&#x2718;</span> No consecutive keyboard letters (e.g., qwerty)
    </div>
    <div :class="{ valid: !isSimilarToUsername, invalid: isSimilarToUsername }">
      <span v-if="!isSimilarToUsername">&#x2714;</span>
      <span v-else>&#x2718;</span> Password is not similar to username
    </div>
    <div :class="{ valid: !isCommonPassword, invalid: isCommonPassword }">
      <span v-if="!isCommonPassword">&#x2714;</span>
      <span v-else>&#x2718;</span> Password is not common
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
      passwordErrors: [],
      passwordValid: false,
      passwordScore: 0,
      passwordScoreMessage: '',
      passwordRequirements: true,
      commonPasswords: ["123456", "password", "123456789", "12345678", "12345", "1234567", "qwerty", 
  "abc123", "password1", "123123", "letmein", "monkey", "1234", "123321", 
  "1q2w3e4r", "qwertyuiop", "111111", "123qwe", "sunshine", "qwerty123", 
  "password123", "princess", "123qwert", "football", "123rty", "iloveyou", 
  "123", "abc123", "qwert", "admin123", "starwars", "1password", "qwertyui", 
  "1qaz2wsx", "letmein1", "trustno1", "dragon", "123abc", "master", "hello123", 
  "password1", "welcome1", "1qazxsw2", "password1234", "password1", "123password", 
  "iloveyou1", "1q2w3e4r5t", "123qwe", "p@ssw0rd", "qazwsx", "11111", "shadow", 
  "baseball", "1q2w3e4r5t", "123123123", "qwerty1", "654321", "superman", 
  "fuckme", "qwerty12345", "123123q", "qwert12345", "123test", "yesterday", 
  "qwert!@#", "hello1", "sunshine1", "dragon123", "love123", "1abcdef", "jackson", 
  "monkey123", "ashley", "baseball123", "123abcd", "123abc321", "1q2w3e4r5t", 
  "test123", "123654", "admin1234", "iloveyou123", "letmein123", "admin12345", 
  "loveme", "princess123", "qwerty12", "12345qwert", "qwert1234", "password11", 
  "secret", "admin1", "superman1", "112233", "iloveyou2", "thepassword", "letmein234", 
  "test", "chocolate", "secret123", "welcome123", "12345abcde", "qwerty1234", "superstar"],
    };
  },
  computed: {
    passwordLengthValid() {
      return this.password.length >= 8;
    },
    hasUppercase() {
      return /[A-Z]/.test(this.password);
    },
    hasLowercase() {
      return /[a-z]/.test(this.password);
    },
    hasNumber() {
      return /[0-9]/.test(this.password);
    },
    hasSpecial() {
      return /[!@#$%^&*(),.?":{}|<>]/.test(this.password);
    },
    hasConsecutiveLetters() {
      const lowercasePassword = this.password.toLowerCase();
      const keyboardLayout = "qwertyuiopasdfghjklzxcvbnm";
      for (let i = 0; i < lowercasePassword.length - 5; i++) {
        const segment = lowercasePassword.slice(i, i + 6);
        if (keyboardLayout.includes(segment)) {
          return true;
        }
      }
      return false;
    },
    isSimilarToUsername() {
      const usernamePart = this.email.split('@')[0].toLowerCase();
      const lowercasePassword = this.password.toLowerCase();
      return lowercasePassword.includes(usernamePart.slice(0, 6));
    },
    isCommonPassword() {
      return this.commonPasswords.includes(this.password.toLowerCase());
    },
  },
  methods: {
    handleLogin() {
      if (this.passwordValid) {
        alert("Login successful!");
      }
    },
    validatePassword() {
      // Reset errors and update validity
      this.passwordErrors = [];

      // Check each validation rule and collect errors
      if (!this.passwordLengthValid) this.passwordErrors.push("Password must be at least 8 characters long.");
      if (!this.hasUppercase) this.passwordErrors.push("Password needs an uppercase letter.");
      if (!this.hasLowercase) this.passwordErrors.push("Password needs a lowercase letter.");
      if (!this.hasNumber) this.passwordErrors.push("Password needs a number.");
      if (!this.hasSpecial) this.passwordErrors.push("Password needs a symbol.");
      if (this.hasConsecutiveLetters) this.passwordErrors.push("Avoid using sequences of consecutive characters (e.g., qwerty).");
      if (this.isSimilarToUsername) this.passwordErrors.push("Password should not be similar to the username.");
      if (this.isCommonPassword) this.passwordErrors.push("Avoid using common passwords.");

      // Update password validity
      this.passwordValid = this.passwordErrors.length === 0;

      // Update password strength score
      this.passwordScore = this.calculatePasswordScore(this.password);
      this.passwordScoreMessage = this.getPasswordStrengthMessage(this.passwordScore);
    },
    calculatePasswordScore(password) {
      let score = 0;
      if (password.length >= 8) score += 2;
      if (password.length >= 12) score += 2;
      if(password.length >= 16) score += 2;
      if (/[a-z]/.test(password)) score += 1;
      if (/[A-Z]/.test(password)) score += 1;
      if (/[0-9]/.test(password)) score += 1;
      if (/[!@#$%^&*(),.?":{}|<>]/.test(password)) score += 1;
      if (this.hasConsecutiveAscii(password)) score -= 2;
      if (this.hasConsecutiveLetters || this.hasRepeatedCharacters(password)) score -= 2;
      
      return Math.max(1, Math.min(score, 10));
    },
    getPasswordStrengthMessage(score) {
      if (score >= 9) return "Very Strong";
      if (score >= 7) return "Strong";
      if (score >= 5) return "Medium";
      if (score >= 3) return "Weak";
      return "Very Weak";
    },
    hasRepeatedCharacters(password) {
      const charCounts = {};
      for (let char of password) {
        charCounts[char] = (charCounts[char] || 0) + 1;
      }
      return Object.values(charCounts).some(count => count > 2); // Deduct if any character repeats more than twice
    },
    hasConsecutiveAscii(password) {
      for (let i = 0; i < password.length - 3; i++) {
        const charCodes = [];
        for (let j = i; j < i + 4; j++) {
          // Convert each character to lowercase and then to ASCII code to avoid case sensitivity
          charCodes.push(password.charCodeAt(j) | 32); // Bitwise OR with 32 converts uppercase to lowercase
        }
        if (this.isConsecutiveAscii(charCodes)) {
          return true;
        }
      }
      return false;
    },
    isConsecutiveAscii(charCodes) {
      // Check if the array of charCodes contains consecutive ASCII characters
      for (let i = 0; i < charCodes.length - 1; i++) {
        if (charCodes[i] + 1 !== charCodes[i + 1]) {
          return false;
        }
      }
      return true;
    },
  }
};
</script>


<style scoped>
.login-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 350px;
  margin: auto;
  padding: 30px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  background-color: #f9f9f9;
  box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.1);
}

h2 {
  margin-bottom: 20px;
  color: #333;
}

.form-group {
  margin-bottom: 20px;
  width: 100%;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #666;
}

input[type="email"],
input[type="password"] {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 14px;
}

button {
  width: 100%;
  padding: 12px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  transition: background-color 0.3s;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #0056b3;
}

.error-message {
  color: red;
  font-size: 12px;
  margin-top: 5px;
}

.score-message {
  color: #007bff;
  font-size: 13px;
  margin-top: 10px;
  text-align: center;
}

.requirements-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 20px;
  gap: 10px;
}

.requirements-container div {
  font-size: 13px;
  padding: 5px 10px;
  border-radius: 4px;
}

.valid {
  background-color: #4caf50;
  color: white;
}

.invalid {
  background-color: #f44336;
  color: white;
}
</style>
