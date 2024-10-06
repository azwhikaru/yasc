<script setup>
import { ref, onMounted } from 'vue';
import { Select, Input, Card, Button, message } from 'ant-design-vue';
import confetti from 'canvas-confetti';

const backendOptions = ref(['azwhikaru', 'Manual']);
const backendUrlMap = {
  'azwhikaru': 'https://sub.azwhikaru.com'
};
const selectedBackendOption = ref(localStorage.getItem('selectedBackendOption') || backendOptions.value[0]);
const customBackendInput = ref(localStorage.getItem('customBackendInput') || '');
const showBackendInput = ref(selectedBackendOption.value === 'Manual');

const ruleOptions = ref(['ACL4SSR Full', 'Manual']);
const ruleUrlMap = {
  'ACL4SSR Full': 'https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full.ini'
};
const selectedRuleOption = ref(localStorage.getItem('selectedRuleOption') || ruleOptions.value[0]);
const customRuleInput = ref(localStorage.getItem('customRuleInput') || '');
const showRuleInput = ref(selectedRuleOption.value === 'Manual');

const clientOptions = ref(['Clash', 'Quantumult X']);
const clientMap = {
  'Clash': 'clash',
  'Quantumult X': 'quanx'
};
const selectedClientOption = ref(localStorage.getItem('selectedClientOption') || clientOptions.value[0]);

const subscriptionUrl = ref(localStorage.getItem('subscriptionUrl') || '');

const handleBackendChange = (value) => {
  selectedBackendOption.value = value.target ? value.target.value : value;
  showBackendInput.value = value === 'Manual';
  localStorage.setItem('selectedBackendOption', selectedBackendOption.value);
};

const handleRuleChange = (value) => {
  selectedRuleOption.value = value.target ? value.target.value : value;
  showRuleInput.value = value === 'Manual';
  localStorage.setItem('selectedRuleOption', selectedRuleOption.value);
};

const handleCustomBackendInput = (value) => {
  customBackendInput.value = value.target ? value.target.value : value;
  localStorage.setItem('customBackendInput', customBackendInput.value);
};

const handleCustomRuleInput = (value) => {
  customRuleInput.value = value.target ? value.target.value : value;
  localStorage.setItem('customRuleInput', customRuleInput.value);
};

const handleClientChange = (value) => {
  selectedClientOption.value = value.target ? value.target.value : value;
  localStorage.setItem('selectedClientOption', selectedClientOption.value);
};

const handleSubscriptionUrlChange = (value) => {
  subscriptionUrl.value = value.target ? value.target.value : value;
  localStorage.setItem('subscriptionUrl', subscriptionUrl.value);
};

const handleGenerate = () => {
  let hasError = false;

  if (!subscriptionUrl.value) {
    message.error('Subscription URL required');
    hasError = true;
  }

  if (selectedBackendOption.value === 'Manual' && !customBackendInput.value) {
    message.error('Backend URL required');
    hasError = true;
  }

  if (selectedRuleOption.value === 'Manual' && !customRuleInput.value) {
    message.error('Ruleset URL required');
    hasError = true;
  }

  if (hasError) {
    return;
  }

  let backendUrl = backendUrlMap[selectedBackendOption.value] || customBackendInput.value;
  if (selectedBackendOption.value === 'Manual' && backendUrl.endsWith('/')) {
    backendUrl = backendUrl.slice(0, -1);
  }
  const ruleUrl = ruleUrlMap[selectedRuleOption.value] || customRuleInput.value;
  const clientType = clientMap[selectedClientOption.value];

  const finalUrl = `${backendUrl}/sub?target=${encodeURIComponent(clientType)}&new_name=true&url=${encodeURIComponent(subscriptionUrl.value)}&insert=false&config=${encodeURIComponent(ruleUrl)}`;

  navigator.clipboard.writeText(finalUrl).then(() => {
    message.success('URL copied to clipboard!');
  });

  confetti();
};

onMounted(() => {
  handleBackendChange(selectedBackendOption.value);
  handleRuleChange(selectedRuleOption.value);
});
</script>

<template>
  <div class="container">
    <a-card class="card" title="YASC">
      <div>
        <h4>Subscription URL</h4>
        <a-input v-model:value="subscriptionUrl" @input="handleSubscriptionUrlChange" placeholder="Subscription URL" />
      </div>

      <div style="margin-top: 10px;">
        <h4>Backend URL</h4>
        <a-select v-model:value="selectedBackendOption" @change="handleBackendChange" style="width: 100%">
          <a-select-option v-for="option in backendOptions" :key="option" :value="option">
            {{ option }}
          </a-select-option>
        </a-select>
        <a-input v-if="showBackendInput" v-model:value="customBackendInput" @input="handleCustomBackendInput" placeholder="Backend URL" style="margin-top: 10px;" />
      </div>

      <div style="margin-top: 10px;">
        <h4>Ruleset URL</h4>
        <a-select v-model:value="selectedRuleOption" @change="handleRuleChange" style="width: 100%">
          <a-select-option v-for="option in ruleOptions" :key="option" :value="option">
            {{ option }}
          </a-select-option>
        </a-select>
        <a-input v-if="showRuleInput" v-model:value="customRuleInput" @input="handleCustomRuleInput" placeholder="Ruleset URL" style="margin-top: 10px;" />
      </div>

      <div style="margin-top: 10px;">
        <h4>Client Type</h4>
        <a-select v-model:value="selectedClientOption" @change="handleClientChange" style="width: 100%">
          <a-select-option v-for="option in clientOptions" :key="option" :value="option">
            {{ option }}
          </a-select-option>
        </a-select>
      </div>

      <div style="margin-top: 20px; display: flex; justify-content: center;">
        <a-button @click="handleGenerate">Generate & Copy</a-button>
      </div>
    </a-card>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f0f0;
  padding: 10px;
  box-sizing: border-box;
}

.card {
  width: 100%;
  max-width: 300px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
</style>
