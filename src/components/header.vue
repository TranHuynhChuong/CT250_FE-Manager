<template>
    <div class="w-full min-h-14 h-14 bg-white shadow-sm flex z-20 fixed">
        <div class="w-48 min-w-48 mt-3 mb-2 m-auto">
            <img src="../public/logo2.png" alt="" class="w-full h-full object-contain" />
        </div>
        <div class="flex-1 flex items-center justify-between">
            <div class="flex flex-1 items-center whitespace-nowrap">
                <span class="pl-4 text-zinc-400 hover:text-zinc-500 text-base">Trang chủ</span>
                <div v-for="(item, index) in breadcrumb" :key="index">
                    <span class="mx-2 text-zinc-400"> > </span>
                    <span
                        :class="[
                            item.current ? 'text-zinc-900' : 'text-zinc-400 hover:text-zinc-500',
                            ' text-base',
                        ]"
                    >
                        {{ item.name }}
                    </span>
                </div>
            </div>
            <div class="h-full w-fit relative ml-5">
                <div
                    class="absolute -left-1 top-1/2 -translate-y-1/2 h-1/2 w-[1px] bg-zinc-300"
                ></div>
                <LogoutButton />
            </div>
        </div>
    </div>
</template>

<script setup>
import { useRoute } from "vue-router";
import { computed } from "vue";

const route = useRoute();

const breadcrumb = computed(() => {
    // Quản lý sản phẩm - Product
    if (route.path.includes("/product/new")) {
        return [{ name: "Sản phẩm" }, { name: "Thêm sản phẩm mới", current: true }];
    } else if (route.path.includes("/product/detail")) {
        return [{ name: "Sản phẩm" }, { name: "Chi tiết sản phẩm", current: true }];
    } else if (route.path.includes("/product/")) {
        return [{ name: "Sản phẩm", current: true }];
    }

    return [];
});
</script>
