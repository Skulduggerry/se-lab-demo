<script setup lang="ts">
import { ref } from 'vue'
import { useCollection } from 'vuefire'
import { collection, deleteDoc, doc, addDoc } from 'firebase/firestore'
import { db } from '@/firebase.ts'

const newMember = ref('')

const membersRef = collection(db, 'members')
const members = useCollection(membersRef)

async function addMember() {
  if (!newMember.value.trim()) return

  try {
    await addDoc(membersRef, {
      name: newMember.value,
    })

    newMember.value = ''
  } catch (e) {
    console.error('Error adding member: ', e)
    alert('Could not add member.')
  }
}

async function removeMember(id: string) {
  try {
    await deleteDoc(doc(db, 'members', id))
  } catch (e) {
    console.error('Error removing member: ', e)
    alert('Failed to delete member')
  }
}
</script>

<template>
  <h1>Hello Team viadee-2</h1>

  <!-- The @submit.prevent ensures the page doesn't reload -->
  <form @submit.prevent="addMember">
    <input v-model="newMember" required placeholder="New Member Name" />
    <button type="submit">Add Member</button>
  </form>

  <ul>
    <li v-for="member in members" :key="member.id">
      {{ member.name }}
      <button @click="removeMember(member.id)">X</button>
    </li>
  </ul>
</template>
