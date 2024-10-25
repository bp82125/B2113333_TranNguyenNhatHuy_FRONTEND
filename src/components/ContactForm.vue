<template>
  <Form @submit="submitContact" :validation-schema="contactFormSchema">
    <div class="form-group">
      <label for="name">Tên</label>
      <Field
        name="name"
        type="text"
        class="form-control"
        v-model="contactLocal.name"
      />
      <ErrorMessage name="name" class="error-feedback" />
    </div>
    <div class="form-group">
      <label for="email">E-mail</label>
      <Field
        name="email"
        type="email"
        class="form-control"
        v-model="contactLocal.email"
      />
      <ErrorMessage name="email" class="error-feedback" />
    </div>
    <div class="form-group">
      <label for="address">Địa chỉ</label>
      <Field
        name="address"
        type="text"
        class="form-control"
        v-model="contactLocal.address"
      />
      <ErrorMessage name="address" class="error-feedback" />
    </div>
    <div class="form-group">
      <label for="phone">Điện thoại</label>
      <Field
        name="phone"
        type="tel"
        class="form-control"
        v-model="contactLocal.phone"
      />
      <ErrorMessage name="phone" class="error-feedback" />
    </div>
    <div class="form-group form-check">
      <input
        name="favorite"
        type="checkbox"
        class="form-check-input"
        v-model="contactLocal.favorite"
      />
      <label for="favorite" class="form-check-label"
        ><strong>Liên hệ yêu thích</strong></label
      >
    </div>
    <div class="form-group d-flex w-100">
      <button class="btn btn-primary w-100">Lưu</button>
      <button
        v-if="contactLocal._id"
        type="button"
        class="ml-2 btn btn-danger w-100"
        @click="deleteContact"
      >
        Xóa
      </button>
      <button type="button" class="ml-2 btn btn-danger w-100" @click="cancel">
        Thoát
      </button>
    </div>
  </Form>
</template>

<script setup>
import { ref, watch } from "vue";
import { Form, Field, ErrorMessage } from "vee-validate";
import * as yup from "yup";
import { useRouter } from "vue-router";

defineEmits(["submit:contact", "delete:contact"]);

const props = defineProps({
  contact: { type: Object, required: true },
});

const router = useRouter();
const contactLocal = ref({ ...props.contact });

const contactFormSchema = yup.object({
  name: yup
    .string()
    .required("Tên phải có giá trị.")
    .min(2, "Tên phải ít nhất 2 ký tự.")
    .max(50, "Tên có nhiều nhất 50 ký tự."),
  email: yup
    .string()
    .email("E-mail không đúng.")
    .max(50, "E-mail tối đa 50 ký tự."),
  address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),
  phone: yup
    .string()
    .matches(/((09|03|07|08|05)+([0-9]{8})\b)/g, "Số điện thoại không hợp lệ."),
});

watch(
  () => props.contact,
  (newContact) => {
    contactLocal.value = { ...newContact };
  }
);

function submitContact() {
  emit("submit:contact", contactLocal.value);
}

function deleteContact() {
  emit("delete:contact", contactLocal.value._id);
}

function cancel() {
  const reply = window.confirm(
    "You have unsaved changes! Do you want to leave?"
  );
  if (reply) {
    router.push({ name: "contactbook" });
  }
}
</script>

<style scoped>
@import "@/assets/form.css";
.error-feedback {
  color: red;
}
</style>
