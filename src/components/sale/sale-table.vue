<template>
    <div class="min-w-fit w-full h-full bg-transparent">
        <div class="w-full h-fit flex justify-between items-center space-x-6">
            <div
                class="flex flex-3/5 items-center gap-2 border border-zinc-400 hover:border-zinc-400 focus-within:border-zinc-500 rounded-md px-3 py-2"
            >
                <span class="text-sm text-zinc-700">Tìm kiếm đơn hàng</span>
                <input
                    v-model="inputValue"
                    @input="handleInput"
                    type="text"
                    class="flex-1 text-sm text-zinc-900 bg-transparent outline-none"
                    placeholder="Nhập mã đơn hàng"
                />
            </div>
            <div class="flex items-center flex-2/5 h-fit space-x-3 justify-end">
                <button
                    @click="search()"
                    class="py-auto px-4 bg-transparent h-9 text-sm text-emerald-500 rounded-md cursor-pointer border-[1px] border-emerald-500 hover:bg-emerald-200/10 box-border"
                >
                    Áp dụng
                </button>
                <button
                    @click="clear()"
                    class="py-auto px-4 bg-transparent h-9 text-sm text-zinc-700 rounded-md cursor-pointer border-[1px] border-zinc-400 hover:bg-zinc-200/70 box-border"
                >
                    Nhập lại
                </button>
            </div>
        </div>

        <div class="mt-5 text-lg font-medium">{{ totalOrder }} Đơn hàng</div>
        <div class="w-full h-fit rounded-md border-[1px] border-zinc-400 mt-5">
            <div class="w-full h-fit p-4 space-y-6">
                <div
                    v-for="(order, index) in orders"
                    :key="index"
                    class="w-full h-fit rounded border-[1px] border-zinc-400 overflow-hidden"
                >
                    <div class="w-full h-fit flex p-4 justify-between bg-zinc-200">
                        <div class="w-fit h-fit">{{ stateOrder[order.trangThai_DH] }}</div>
                    </div>
                    <div class="w-full h-fit flex py-6">
                        <div class="w-2/3 h-fit">
                            <div
                                v-for="(detail, idx) in order.chiTiet_DH"
                                :key="idx"
                                class="w-full h-fit flex px-6"
                            >
                                <div class="w-full flex border-b-[1px] py-4">
                                    <div class="w-2/5 h-fit">
                                        <div>{{ detail.tenSanPham_CTDH }}</div>
                                        <div>{{ detail.ttBanHang_CTDH }}</div>
                                        <div>{{ formattedMaSP(detail.maSanPham_CTDH) }}</div>
                                    </div>
                                    <div class="w-1/5 h-fit">{{ detail.giaMua_CTDH }}</div>
                                    <div class="w-1/5 h-fit">x {{ detail.soLuong_CTDH }}</div>
                                    <div class="w-1/5 h-fit font-medium">
                                        {{ detail.giaMua_CTDH * detail.soLuong_CTDH }}
                                    </div>
                                </div>
                            </div>
                            <div class="w-full h-fit flex flex-col px-6">
                                <div class="w-full flex py-2 font-medium">
                                    <div class="w-2/5 h-fit">Thành tiền</div>
                                    <div class="w-1/5 h-fit"></div>
                                    <div class="w-1/5 h-fit">{{ totalQuantity(order) }}</div>
                                    <div class="w-1/5 h-fit">{{ totalPrice(order) }}</div>
                                </div>
                                <div class="w-full flex py-2 font-medium">
                                    <div class="w-2/5 h-fit">Phí vận chuyển</div>
                                    <div class="w-1/5 h-fit"></div>
                                    <div class="w-1/5 h-fit"></div>
                                    <div class="w-1/5 h-fit">{{ order.vanChuyen_DH }}</div>
                                </div>
                                <div class="w-full flex py-2 font-medium">
                                    <div class="w-2/5 h-fit">Tổng</div>
                                    <div class="w-1/5 h-fit"></div>
                                    <div class="w-1/5 h-fit"></div>
                                    <div class="w-1/5 h-fit">
                                        {{ order.vanChuyen_DH + totalPrice(order) }}
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="w-1/3 h-full px-6 space-y-2 pt-2 border-l-[1px]">
                            <div class="w-full h-fit">Mã đơn hàng : {{ order.ma_DH }}</div>
                            <div class="w-full h-fit">
                                Số điện thoại :
                                {{ order.ttNhanHang_DH.sdt_NH }}
                            </div>
                            <div class="w-full h-fit">
                                Tên :
                                {{ order.ttNhanHang_DH.hoTen_NH }}
                            </div>
                            <div class="w-full h-fit">
                                Địa chỉ :
                                {{ order.ttNhanHang_DH.diaChi_NH.chiTiet_DC }},
                                {{ order.ttNhanHang_DH.diaChi_NH.xa_DC }},
                                {{ order.ttNhanHang_DH.diaChi_NH.huyen_DC }},
                                {{ order.ttNhanHang_DH.diaChi_NH.tinh_DC }}
                            </div>
                            <div class="w-full h-fit">
                                Ngày tạo: {{ formatDate(order.ngayTao_DH) }}
                            </div>
                            <div class="w-full h-fit">
                                Ngày cập nhật: {{ formatDate(order.ngayCapNhat_DH) }}
                            </div>
                        </div>
                    </div>
                    <div class="w-full h-fit flex p-6 justify-end bg-zinc-100">
                        <div
                            v-if="order.trangThai_DH == 1"
                            @click="updateState('2', order._id)"
                            class="px-6 py-3 bg-emerald-300 rounded-md cursor-pointer"
                        >
                            Xác nhận đơn hàng
                        </div>
                        <div
                            v-else-if="order.trangThai_DH == 2"
                            @click="updateState('3', order._id)"
                            class="px-6 py-3 bg-emerald-300 rounded-md cursor-pointer"
                        >
                            Vận chuyển
                        </div>
                        <div
                            v-else-if="order.trangThai_DH == 3"
                            @click="updateState('4', order._id)"
                            class="px-6 py-3 bg-emerald-300 rounded-md cursor-pointer"
                        >
                            Đã giao
                        </div>
                        <div
                            v-else-if="order.trangThai_DH == 5"
                            @click="updateState('6', order._id)"
                            class="px-6 py-3 bg-emerald-300 rounded-md cursor-pointer"
                        >
                            Xác nhận hủy đơn
                        </div>
                    </div>
                </div>
            </div>
            <div class="w-full h-fit p-6">
                <div class="flex justify-between items-center">
                    <span>Trang {{ currentPage }} / {{ totalPages }}</span>

                    <div class="flex space-x-2">
                        <button
                            @click="prevPage"
                            :disabled="currentPage === 1"
                            class="rounded-md disabled:cursor-not-allowed w-fit h-10 flex items-center cursor-pointer"
                        >
                            <Icon name="ic:round-keyboard-arrow-left" size="24" />
                        </button>
                        <div class="relative">
                            <button
                                @click="toggleDropdown"
                                class="bg-zinc-100 rounded-md w-14 h-10 flex items-center justify-center cursor-pointer"
                            >
                                {{ currentPage }}
                            </button>
                            <div
                                v-if="showDropdown"
                                class="absolute right-0 bottom-full w-full bg-white border-[1px] border-zinc-400 rounded-md shadow-md max-h-[150px] overflow-y-auto"
                            >
                                <div
                                    v-for="page in totalPages"
                                    :key="page"
                                    @click="changePage(page)"
                                    class="px-4 py-2 hover:bg-gray-100 cursor-pointer"
                                >
                                    {{ page }}
                                </div>
                            </div>
                        </div>
                        <button
                            @click="nextPage"
                            :disabled="currentPage === totalPages"
                            class="rounded-md disabled:cursor-not-allowed w-fit h-10 flex items-center cursor-pointer"
                        >
                            <Icon name="ic:round-keyboard-arrow-right" size="24" />
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
import { ref, computed, onMounted } from "vue";
import Swal from "sweetalert2";

import { format } from "date-fns";
import vi from "date-fns/locale/vi"; // Nếu muốn hiển thị tiếng Việt

const formatDate = (dateString) => {
    return format(new Date(dateString), "dd/MM/yyyy HH:mm:ss", { locale: vi });
};

const inputValue = ref("");
const stateOrder = [
    "Tất cả",
    "Chờ xác nhận",
    "Chờ vận chuyển",
    "Đang giao",
    "Đã giao",
    "Yêu cầu hủy",
];

const clear = () => {
    inputValue.value = "";
    getOrders(0);
    currentPage.value = 1;
};

const search = () => {
    if (inputValue.value != "") {
        findOrders(0);
        currentPage.value = 1;
    }
};

const totalOrder = ref(0);
const orders = ref([]);

const perPage = 12;
const currentPage = ref(1);
const showDropdown = ref(false);

// Tổng số trang
const totalPages = computed(() => Math.ceil(totalOrder.value / perPage));

// Đổi trang
const changePage = (page) => {
    if (page >= 1 && page <= totalPages.value) {
        currentPage.value = page;
        showDropdown.value = false;
        console.log(currentPage);
        if (inputValue.value != "") {
            findOrders(currentPage.value - 1);
        } else {
            getOrders(currentPage.value - 1);
        }
    }
};
// Nút trước / sau
const prevPage = () => {
    currentPage.value > 1 && currentPage.value--;
    console.log(currentPage);
    if (inputValue.value != "") {
        findOrders(currentPage.value - 1);
    } else {
        getOrders(currentPage.value - 1);
    }
};
const nextPage = () => {
    currentPage.value < totalPages.value && currentPage.value++;
    console.log(currentPage);
    if (inputValue.value != "") {
        findOrders(currentPage.value - 1);
    } else {
        getOrders(currentPage.value - 1);
    }
};

// Toggle dropdown
const toggleDropdown = () => {
    showDropdown.value = !showDropdown.value;
};

const totalPrice = (order) => {
    return order.chiTiet_DH.reduce((tong, item) => tong + item.soLuong_CTDH * item.giaMua_CTDH, 0);
};

const totalQuantity = (order) => {
    return order.chiTiet_DH.reduce((tong, item) => tong + item.soLuong_CTDH, 0);
};

const formattedMaSP = (code) => {
    return code.toString().padStart(8, "0");
};

const authStore = useAuthStore();
const { $api } = useNuxtApp(); // ✅ Truy cập api từ plugin

import { useRouter, useRoute } from "vue-router";

const route = useRoute();
const state = computed(() => {
    if (route.path.includes("/sale/all")) return 0;
    if (route.path.includes("/sale/unpaid")) return 1;
    if (route.path.includes("/sale/processing")) return 2;
    if (route.path.includes("/sale/shipping")) return 3;
    if (route.path.includes("/sale/complete")) return 4;
    if (route.path.includes("/sale/cancelRequest")) return 5;

    return null; // Mặc định nếu không khớp
});

async function getOrders(page) {
    try {
        const res = await $api.get("order", {
            params: {
                page: String(page),
                state: state.value !== null ? String(state.value) : 0,
            },
        });
        if (res.data.success == true) {
            orders.value = res.data.data.orders;
            if (page == 0) {
                totalOrder.value = res.data.data.total;
            }
        }
    } catch (error) {
        console.error("Lỗi:", error);
    }
}

async function updateState(state, id) {
    try {
        const res = await $api.put(`order/${id}`, null, { params: { state } });

        if (res.data.success == true) {
            await getOrders(0);
        }
    } catch (error) {
        console.error("Lỗi:", error);
    }
}

onMounted(async () => {
    await getOrders(0);
});
</script>
