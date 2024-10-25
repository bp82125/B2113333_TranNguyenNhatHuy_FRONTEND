<template>
  <div v-if="contact" class="page">
    <h4>Hiệu chỉnh Liên hệ</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="updateContact"
      @delete:contact="deleteContact"
      :isEditMode="true"
    />
    <p>{{ message }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter, useRoute } from "vue-router";
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

const props = defineProps({
  id: { type: String, required: true },
});

const router = useRouter();
const route = useRoute();

const contact = ref(null);
const message = ref("");

async function getContact(id) {
  try {
    contact.value = await ContactService.get(id);
  } catch (error) {
    console.error(error);
    router.push({
      name: "notfound",
      params: { pathMatch: route.path.split("/").slice(1) },
      query: route.query,
      hash: route.hash,
    });
  }
}

async function updateContact(data) {
  try {
    await ContactService.update(contact.value._id, data);
    alert("Liên hệ được cập nhật thành công.");
    router.push({ name: "contactbook" });
  } catch (error) {
    console.error(error);
  }
}

async function deleteContact() {
  if (confirm("Bạn muốn xóa Liên hệ này?")) {
    try {
      await ContactService.delete(contact.value._id);
      router.push({ name: "contactbook" });
    } catch (error) {
      console.error(error);
    }
  }
}

onMounted(() => {
  getContact(props.id);
  message.value = "";
});
</script>

<style scoped>
.page {
  padding: 20px;
}
</style>
