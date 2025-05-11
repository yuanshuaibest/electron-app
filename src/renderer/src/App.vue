<script setup lang="ts">
import Versions from './components/Versions.vue'
import { ref, onMounted } from 'vue'

const nowTime = ref('')
const userInfo = ref<{
  name: string
  age: number
  sex: string
  address: string
}>()
const phone = ref('')

const ipcHandle = (): void => window.electron.ipcRenderer.send('ping')
const ipcHandle2 = async (): Promise<void> => {
  const res = await window.electron.ipcRenderer.sendSync('getUserInfo')
  console.log(res)
  userInfo.value = res
}
const ipcHandle3 = async (): Promise<void> => {
  const res = await window.electron.ipcRenderer.invoke('getPhone')
  console.log(res)
  phone.value = res
}

onMounted(() => {
  window.electron.ipcRenderer.on('messageToRenderer', (event, arg) => {
    console.log(event, arg)
    nowTime.value = arg
  })

  window.electron.ipcRenderer.on('ping replay', (event, arg) => {
    console.log(event, arg)
  })

  ipcHandle2()
  ipcHandle3()
})
</script>

<template>
  <img alt="logo" class="logo" src="./assets/electron.svg" />
  <div class="creator">Powered by electron-vite</div>
  <div class="text">
    Build an Electron app with
    <span class="vue">Vue</span>
    and
    <span class="ts">TypeScript</span>
  </div>
  <p class="tip">Please try pressing <code>F12</code> to open the devTool</p>
  <p class="tip">Now Time : {{ nowTime }}</p>
  <p class="tip">User Name : {{ userInfo?.name || '' }}</p>
  <p class="tip">Phone Number : {{ phone || '' }}</p>
  <div class="actions">
    <div class="action">
      <a href="https://electron-vite.org/" target="_blank" rel="noreferrer">Documentation</a>
    </div>
    <div class="action">
      <a target="_blank" rel="noreferrer" @click="ipcHandle">Send IPC</a>
    </div>
  </div>
  <Versions />
</template>
