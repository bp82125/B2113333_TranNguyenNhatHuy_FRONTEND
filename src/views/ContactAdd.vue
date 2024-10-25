<template>
  <div class="page">
    <h4>Thêm Liên hệ Mới</h4>
    <ContactForm @submit:contact="createContact" :isEditMode="false" />
    <p>{{ message }}</p>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

const router = useRouter();
const message = ref("");

async function createContact(data) {
  try {
    await ContactService.create(data);
    alert("Liên hệ mới đã được thêm thành công.");
    router.push({ name: "contactbook" });
  } catch (error) {
    console.error(error);
    message.value = "Có lỗi xảy ra khi thêm liên hệ.";
  }
}
</script>

<style scoped>
.page {
  padding: 20px;
}
</style>
