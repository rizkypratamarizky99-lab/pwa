
  <!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Aplikasi POS Kasir Modern</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    
    <style>
        /* --- CSS (DESAIN) --- */
        body { font-family: sans-serif; background: #f4f4f4; padding: 20px; }
        .pos-wrapper { display: flex; gap: 20px; max-width: 1100px; margin: auto; }
        
        /* Kolom Produk */
        .produk-box { flex: 2; background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 15px; }
        .item { border: 1px solid #ddd; padding: 10px; text-align: center; border-radius: 8px; }
        
        /* Kolom Kasir */
        .kasir-box { flex: 1; background: #2c3e50; color: white; padding: 20px; border-radius: 10px; }
        .keranjang-item { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 0.9em; }
        .total { border-top: 2px solid white; margin-top: 20px; padding-top: 10px; font-size: 1.5em; }
        
        /* Tombol & QRIS */
        button { width: 100%; padding: 10px; cursor: pointer; border: none; border-radius: 5px; background: #27ae60; color: white; }
        button:disabled { background: #95a5a6; }
        .qris-area { background: white; color: black; margin-top: 20px; padding: 15px; text-align: center; border-radius: 8px; }
        .qris-area img { width: 150px; }
    </style>
</head>
<body>

<div id="app" class="pos-wrapper">
    <div class="produk-box">
        <h2>Daftar Produk</h2>
        <div class="grid">
            <div v-for="p in products" :key="p.id" class="item">
                <h3>{{ p.nama }}</h3>
                <p>Rp {{ p.harga.toLocaleString() }}</p>
                <p><small>Stok: {{ p.stok }}</small></p>
                <button @click="tambah(p)" :disabled="p.stok <= 0">Pilih</button>
            </div>
        </div>
    </div>

    <div class="kasir-box">
        <h2>Kasir</h2>
        <div v-for="(item, idx) in keranjang" :key="idx" class="keranjang-item">
            <span>{{ item.nama }}</span>
            <span>Rp {{ item.harga.toLocaleString() }}</span>
        </div>

        <div class="total">
            Total: Rp {{ totalBayar.toLocaleString() }}
        </div>

        <div v-if="totalBayar > 0" class="qris-area">
            <p><strong>Scan QRIS untuk Bayar</strong></p>
            <img :src="'https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=PAY-' + totalBayar" alt="QRIS">
            <button @click="bayar" style="margin-top:10px">Konfirmasi Lunas</button>
        </div>
    </div>
</div>

<script>
    // --- VUE.JS (LOGIKA) ---
    const { createApp, ref, computed } = Vue;

    createApp({
        setup() {
            const products = ref([
                { id: 1, nama: 'Kopi Susu', harga: 15000, stok: 10 },
                { id: 2, nama: 'Roti Bakar', harga: 12000, stok: 5 },
                { id: 3, nama: 'Teh Manis', harga: 5000, stok: 20 }
            ]);

            const keranjang = ref([]);

            const tambah = (produk) => {
                keranjang.value.push(produk);
                produk.stok--; // Stok berkurang di layar
            };

            const totalBayar = computed(() => {
                return keranjang.value.reduce((total, item) => total + item.harga, 0);
            });

            const bayar = () => {
                alert("Pembayaran Berhasil! Mencetak struk...");
                keranjang.value = []; // Kosongkan keranjang
            };

            return { products, keranjang, tambah, totalBayar, bayar };
        }
    }).mount('#app');
</script>

</body>
</html>
