<template>
    <CategoryForm @submit="handleSubmit"></CategoryForm>
</template>
<script setup>
import { useRouter } from "vue-router";

const router = useRouter();

const handleSubmit = (nganhHang) => {
    addCategory(nganhHang);
};

const authStore = useAuthStore();
const { $api } = useNuxtApp(); // ✅ Truy cập api từ plugin

async function addCategory(data) {
    try {
        const res = await $api.post("/categories", data);
        if (res.data.success) {
            router.push("/category");
            showSuccess();
        } else {
            showError();
        }
    } catch (error) {
        console.error("Lỗi:", error);
        showError();
    }
}

import { useToast } from "vue-toastification";
const toast = useToast();

const showSuccess = () => {
    toast.success("Ngành hàng được thêm thành công");
};

const showError = () => {
    toast.error("Có lỗi xảy ra, không thể thêm ngành hàng!");
};
</script>
