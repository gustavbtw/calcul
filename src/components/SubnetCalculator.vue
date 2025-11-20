<template>
  <div class="calculator">
    <h2>Калькулятор подсетей</h2>
    
    <div class="form">
      <div class="field">
        <label for="ip">IP адрес:</label>
        <input
          id="ip"
          v-model="ipAddress"
          type="text"
          placeholder="192.168.1.150"
          class="input"
          @keyup.enter="calculate"
        >
        <span v-if="!isIpValid(ipAddress) && ipAddress" class="error">
          Неверный формат IP адреса
        </span>
      </div>

      <div class="field">
        <label for="mask">Маска подсети:</label>
        <select
          id="mask"
          v-model="selectedMask"
          class="select"
        >
          <option
            v-for="mask in SUBNET_MASKS"
            :key="mask"
            :value="mask"
          >
            {{ mask }}
          </option>
        </select>
      </div>

      <button
        :disabled="!isFormValid"
        class="button"
        @click="calculate"
      >
        Рассчитать
      </button>
    </div>

    <div v-if="results" class="results">
      <h3>Результаты:</h3>
      <div class="result-item">
        <strong>IP адрес:</strong> {{ results.ip }}
      </div>
      <div class="result-item">
        <strong>Маска подсети:</strong> {{ results.mask }}
      </div>
      <div class="result-item">
        <strong>Адрес сети:</strong> {{ results.networkAddress }}
      </div>
      <div class="result-item">
        <strong>Количество адресов:</strong> {{ results.addressesCount }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { isIpValid } from '../utils/ipValidation'
import { getNetworkAddress, getAddressesCount } from '../utils/networkCalculations'
import { SUBNET_MASKS } from '../constants/subnetMasks'

const ipAddress = ref('')
const selectedMask = ref(SUBNET_MASKS[8]) // 255.255.255.0 по умолчанию
const results = ref<{
  ip: string
  mask: string
  networkAddress: string
  addressesCount: number
} | null>(null)

const isFormValid = computed(() => {
  return isIpValid(ipAddress.value)
})

function calculate() {
  if (!isFormValid.value) return

  results.value = {
    ip: ipAddress.value,
    mask: selectedMask.value,
    networkAddress: getNetworkAddress(ipAddress.value, selectedMask.value),
    addressesCount: getAddressesCount(selectedMask.value)
  }
}
</script>

<style scoped>
.calculator {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

label {
  font-weight: bold;
  color: var(--color-primary);
}

.input,
.select {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.input:focus,
.select:focus {
  outline: none;
  border-color: var(--color-primary);
}

.button {
  padding: 12px 24px;
  background-color: var(--color-primary);
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

.button:disabled {
  background-color: var(--color-gray);
  cursor: not-allowed;
}

.button:hover:not(:disabled) {
  background-color: #0056b3;
}

.error {
  color: var(--color-error);
  font-size: 14px;
}

.results {
  margin-top: 20px;
  padding: 16px;
  background-color: white;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.result-item {
  margin: 8px 0;
  padding: 4px 0;
}
</style>