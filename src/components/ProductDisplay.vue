<template>
    <div>
        <div v-if="!isFinished">
            <!-- Bagian konten utama -->
            <div :class="bodyClass">

                <!-- mengatur class agar dinamis dengan binding -->
                <div class="product"
                    :class="{ 'men-clothing': product && product.category === 'men\'s clothing', 'women-clothing': product && product.category === 'women\'s clothing' }"
                    v-if="product">

                    <!-- Bagian kiri produk -->
                    <div class="product-left">
                        <img :src="product.image" :alt="product.title" class="product-image">
                    </div>

                    <!-- Bagian kanan produk -->
                    <div class="product-right">
                        <h3 class="product-title">{{ product.title }}</h3>
                        <div class="rat-cat">
                            <p class="product-category">{{ product.category }}</p>
                            <p class="product-rating">Rating: {{ product.rating.rate }} / 5</p>
                        </div>
                        <p class="product-description">{{ product.description }}</p>
                        <p class="product-price">Price: {{ product.price }}</p>

                        <!-- Form pembelian -->
                        <div v-if="showBuyForm">
                            <form @submit.prevent="submitForm">
                                <fieldset class="form">
                                    <legend>Please Fill This Form!</legend>

                                    <div class="username">
                                        <div class="firstName">
                                            <label for="firstName">First Name:</label><br>
                                            <input id="firstName" type="text" v-model="firstName" required>
                                        </div>
                                        <div class="lastName">
                                            <label for="lastName">Last Name:</label><br>
                                            <input id="lastName" type="text" v-model="lastName" required>
                                        </div>
                                    </div>

                                    <div class="address">
                                        <label for="address">Address:</label><br>
                                        <input id="address" type="text" v-model="address" required>
                                    </div>

                                    <br>
                                    <div class="button-form">
                                        <button type="submit" class="submit-oke">Submit</button>
                                        <button type="button" class="submit-cancel" @click="cancelForm">Cancel</button>
                                    </div>
                                </fieldset>
                            </form>
                        </div>
                        <!-- Tombol Buy dan Next -->
                        <button class="buy-button" @click="showForm" v-if="!showBuyForm">Buy Now</button>
                        <button class="next-button" @click="getNextProduct"
                            v-if="product && currentIndex < totalProducts - 1 && !showBuyForm">Next</button>
                    </div>
                </div>

                <!-- Loader ketika memuat data -->
                <div class="loader" v-else>
                    <img src="../assets/ripple.gif" alt="spinner">
                    <p>Periksa Jaringan Anda!</p>
                </div>
            </div>
        </div>

        <div v-else class="body-default">
            <!-- Pesan jika produk telah selesai -->
            <div class="product-message">
                <p>Product has finished. Please go back to the first page.</p>
                <img src="../assets/ghost.gif" alt="ghost">
                <button class="back-button" @click="resetPage">Back to First Page</button>
            </div>
        </div>
    </div>
</template>
  
<script>
export default {
    data() {
        return {
            apiUrl: "https://fakestoreapi.com/products",
            currentIndex: 0,
            product: null,
            isLoading: true,
            totalProducts: 20,
            isFinished: false,
            showBuyForm: false,
            firstName: "",
            lastName: "",
            address: "",
        };
    },
    mounted() {
        this.getProduct();
    },
    computed: {
        bodyClass() {
            // Mengatur kelas untuk bagian body berdasarkan kategori produk
            if (this.product) {
                if (this.product.category === "men's clothing") {
                    return 'body-men-clothing';
                } else if (this.product.category === "women's clothing") {
                    return 'body-women-clothing';
                }
            }
            return 'body-default';
        }
    },
    methods: {
        getProduct() {
            this.isLoading = true;
            // Mengambil data produk dari API menggunakan fetch
            fetch(`${this.apiUrl}/${this.currentIndex + 1}`)
                .then(response => response.json())
                .then(data => {
                    this.product = data;
                    this.isLoading = false;
                    if (this.currentIndex === this.totalProducts - 1) {
                        this.isFinished = true;
                    }
                })
                .catch(error => {
                    console.error("Error:", error);
                    this.isLoading = false;
                });
        },
        getNextProduct() {
            // Mengambil produk berikutnya
            this.currentIndex = (this.currentIndex + 1) % this.totalProducts;
            this.getProduct();
        },
        resetPage() {
            // Mereset halaman dan mengambil produk pertama
            this.isFinished = false;
            this.currentIndex = 0;
            this.getProduct();
        },
        showForm() {
            // Menampilkan form pembelian
            this.showBuyForm = true;
        },
        cancelForm() {
            // Membatalkan form pembelian dan menghapus data yang telah diisi
            this.showBuyForm = false;
            this.firstName = "";
            this.lastName = "";
            this.address = "";
        },
        submitForm() {
            // Mengirimkan data form dan menampilkan pesan konfirmasi
            const fullName = this.firstName + " " + this.lastName;
            const alertMessage = `Thank you for your purchase!\n\nProduct: ${this.product.title}\nPrice: ${this.product.price}\n\nName: ${fullName}\nAddress: ${this.address}`;
            alert(alertMessage);
            this.showBuyForm = false;
            this.firstName = "";
            this.lastName = "";
            this.address = "";
        }
    }
};
</script>
  
<style src="../assets/style/page.css" scoped></style>
  