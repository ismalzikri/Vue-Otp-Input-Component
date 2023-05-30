<script setup>
import OtpInput from './components/OtpInput.vue'
import { ref } from 'vue'

const otp = ref('')

function onChange(value) {
  otp.value = value
}
</script>

<template>
  <div class="container">
    <h1>Proof Of Concept</h1>
    <OtpInput :value="otp" :valueLength="4" @change="onChange" />
    <section>
      <h2>Feature :</h2>
      <ul>
        <li><b>Autofocus</b> cursor when first render.</li>
        <li>Validation first item input <b>required</b>.</li>
        <li><b>Auto-move</b> cursor to next input.</li>
        <li><b>Auto-trigger</b> something when the last input field filled</li>
        <li>Bottom radar <b>active</b> change color when input is filled or not.</li>
      </ul>
    </section>
    <footer class="footer">
      <div style="display: flex; flex-direction: column; gap: 8px">
        <a href="https://www.tiket.com/login?ref=https://www.tiket.com/">
          Tiket.com OTP Experience
        </a>
        <a href="https://github.com/Laravel007/Vue-Otp-Input-Component"> Ismal Zikri </a>
      </div>
    </footer>
  </div>
</template>
