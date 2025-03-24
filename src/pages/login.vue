<template>
    <NuxtLayout>
        <div
            class="flex flex-row flex-1 rounded-xl shadow-xl bg-white/10 backdrop-blur-md m-5 sm:m-20"
        >
            <div class="flex flex-1 justify-center items-center py-10">
                <div class="w-full h-full lg:flex hidden justify-center items-center">
                    <!-- <img src="../public/logo1.png" alt="" class="h-full object-contain" /> -->
                </div>
            </div>
            <div class="max-w-xl w-full min-w-sm h-full min-h-fit bg-white rounded-xl py-15 px-10">
                <div class="w-full h-full min-h-fit flex flex-col justify-center">
                    <h2 class="font-semibold text-3xl text-center text-emerald-400 pb-5">
                        ĐĂNG NHẬP
                    </h2>
                    <form @submit.prevent="login">
                        <div class="my-5">
                            <label class="block mb-2">Email</label>
                            <input
                                v-model="email"
                                type="text"
                                required
                                placeholder="Nhập email"
                                class="w-full p-3 rounded-lg bg-zinc-100 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-emerald-400"
                            />
                        </div>

                        <div class="mb-4">
                            <label class="block mb-2">Mật khẩu</label>
                            <input
                                v-model="password"
                                type="password"
                                required
                                placeholder="Nhập mật khẩu"
                                class="w-full p-3 rounded-lg bg-zinc-100 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-emerald-400"
                            />
                        </div>

                        <!-- <p class="text-emerald-400 text-center mt-2">
                            Email hoặc mật khẩu không đúng
                        </p> -->
                        <button
                            type="submit"
                            class="w-full bg-emerald-400 hover:bg-emerald-500 font-semibold p-3 rounded-lg transition mt-8 cursor-pointer"
                        >
                            Đăng nhập
                        </button>
                    </form>

                    <p class="text-gray-300 text-center mt-4">
                        <NuxtLink
                            to="/forgetPassword"
                            class="text-emerald-400 hover:underline cursor-pointer"
                            >Quên mật khẩu ?</NuxtLink
                        >
                    </p>
                </div>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
definePageMeta({
    layout: "login",
});

const authStore = useAuthStore();
const { $api } = useNuxtApp(); // ✅ Truy cập api từ plugin

// 🛠 Gọi API đăng nhập
async function login() {
    try {
        const res = await $api.post("/auth/customer-login", {
            email: "abc",
            password: "123",
        });
        const data = res.data;
        if (data.accessToken && data.userId) {
            authStore.setAuth(data.accessToken, data.userId); // ✅ Lưu vào Pinia store
        }
        console.log("Đăng nhập thành công:", data);
    } catch (error) {
        console.error("Lỗi đăng nhập:", error);
    }
}

async function logout() {
    try {
        const res = await $api.get("/auth/logout", {});
        authStore.logout();
        console.log("Đăng xuất thành công:", res.data);
    } catch (error) {
        console.error("Lỗi đăng xuất:", error);
    }
}
</script>
