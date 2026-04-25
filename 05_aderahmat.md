

 <!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Sistem POS Modern - Integrated</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        /* --- CSS BASE & VARIABLES --- */
        :root { 
            --primary: #18bc9c; 
            --dark: #2c3e50; 
            --light: #f4f7f6; 
            --neon: #45f3ff;
            --pink: #ff2770;
        }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body { background: #1c1c1c; color: white; min-height: 100vh; overflow-x: hidden; }

        /* --- 1. CSS LOGIN (Glowing Effect) --- */
        .login-wrapper { display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        .login-box { position: relative; width: 380px; height: 420px; background: #1c1c1c; border-radius: 8px; overflow: hidden; }
        .login-box::before { 
            content: ''; position: absolute; top: -50%; left: -50%; width: 380px; height: 420px; 
            background: linear-gradient(0deg, transparent, var(--neon), var(--neon)); 
            transform-origin: bottom right; animation: animate 6s linear infinite; 
        }
        .login-box::after { 
            content: ''; position: absolute; top: -50%; left: -50%; width: 380px; height: 420px; 
            background: linear-gradient(0deg, transparent, var(--pink), var(--pink)); 
            transform-origin: bottom right; animation: animate 6s linear infinite; animation-delay: -3s; 
        }
        @keyframes animate { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        
        .form-login { position: absolute; inset: 2px; background: #28292d; border-radius: 8px; z-index: 10; padding: 50px 40px; display: flex; flex-direction: column; }
        .form-login h2 { text-align: center; color: var(--neon); margin-bottom: 20px; letter-spacing: 0.1em; }
        .form-login input { width: 100%; padding: 10px; margin-bottom: 20px; background: #333; border: none; color: white; border-radius: 4px; outline: none; border: 1px solid #444; }
        .form-login input:focus { border-color: var(--neon); }
        .btn-login { background: var(--neon); color: #1c1c1c; font-weight: bold; cursor: pointer; border: none; padding: 12px; border-radius: 4px; transition: 0.3s; }
        .btn-login:hover { box-shadow: 0 0 15px var(--neon); }

        /* --- 2. CSS DASHBOARD --- */
        .container { padding: 30px; max-width: 1100px; margin: auto; }
        .dashboard-header { margin-bottom: 30px; border-left: 5px solid var(--neon); padding-left: 15px; }
        .menu-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px; }
        .card-menu { background: #28292d; padding: 40px 20px; border-radius: 15px; text-align: center; border: 1px solid #444; cursor: pointer; transition: 0.3s; }
        .card-menu:hover { border-color: var(--neon); transform: translateY(-10px); background: #323339; }
        .card-icon { font-size: 50px; margin-bottom: 15px; display: block; }

        /* --- 3. CSS POS LAYOUT --- */
        .pos-container { display: flex; gap: 20px; height: 85vh; }
        .product-board { flex: 2; background: white; padding: 20px; border-radius: 15px; overflow-y: auto; color: var(--dark); }
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); gap: 15px; }
        .product-card { border: 1px solid #eee; padding: 15px; border-radius: 12px; text-align: center; transition: 0.2s; background: #fff; }
        .product-card:hover { border-color: var(--primary); transform: translateY(-3px); box-shadow: 0 5px 10px rgba(0,0,0,0.05); }
        
        .checkout-panel { flex: 1; background: var(--dark); color: white; padding: 25px; border-radius: 15px; display: flex; flex-direction: column; }
        .cart-item { display: flex; justify-content: space-between; margin-bottom: 12px; padding-bottom: 8px; border-bottom: 1px solid #3e5871; font-size: 0.9em; }
        .total-section { margin-top: auto; font-size: 1.8em; font-weight: bold; text-align: right; border-top: 2px dashed #555; padding-top: 15px; }

        /* Buttons */
        .btn { width: 100%; padding: 15px; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; margin-top: 15px; font-size: 1em; transition: 0.3s; }
        .btn-add { background: var(--primary); color: white; padding: 8px; font-size: 0.9em; }
        .btn-pay { background: #f1c40f; color: #2c3e50; }
        .btn-pay:hover { background: #d4ac0d; }
        .btn-back { background: transparent; color: var(--neon); border: 1px solid var(--neon); width: auto; padding: 8px 15px; margin: 0 0 20px 0; }

        /* --- 4. QRIS MODAL --- */
        .overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); display: flex; align-items: center; justify-content: center; z-index: 1000; }
        .qris-modal { background: white; padding: 30px; border-radius: 20px; text-align: center; color: #333; max-width: 350px; }
        .qris-logo { width: 120px; margin-bottom: 15px; }
        .qr-image { width: 220px; border: 5px solid #f4f4f4; border-radius: 10px; margin: 10px 0; }
    </style>
</head>
<body>

<div id="app">
    <div v-if="page === 'login'" class="login-wrapper">
        <div class="login-box">
            <div class="form-login">
                <h2>SIGN IN</h2>
                <input type="text" placeholder="Username" v-model="username">
                <input type="password" placeholder="Password" v-model="password">
                <button class="btn-login" @click="doLogin">LOGIN SYSTEM</button>
            </div>
        </div>
    </div>

    <div v-if="page === 'dashboard'" class="container">
        <div class="dashboard-header">
            <h1>Selamat Datang, Admin</h1>
            <p>Silahkan pilih modul aplikasi untuk memulai</p>
        </div>
        
        <div class="menu-grid">
            <div class="card-menu" @click="page = 'pos'">
                <span class="card-icon">🛒</span>
                <h3>Buka Kasir (POS)</h3>
                <p><small style="opacity: 0.6;">Transaksi & Pembayaran QRIS</small></p>
            </div>
            <div class="card-menu" @click="alert('Fitur Stok Produk segera hadir')">
                <span class="card-icon">📦</span>
                <h3>Stok Produk</h3>
                <p><small style="opacity: 0.6;">Manajemen Inventori</small></p>
            </div>
            <div class="card-menu" @click="page = 'login'">
                <span class="card-icon">🚪</span>
                <h3>Keluar (Logout)</h3>
                <p><small style="opacity: 0.6;">Selesaikan Sesi Ini</small></p>
            </div>
        </div>
    </div>

    <div v-if="page === 'pos'" class="container">
        <button class="btn btn-back" @click="page = 'dashboard'">⬅ Kembali ke Dashboard</button>
        
        <div class="pos-container">
            <div class="product-board">
                <h2 style="margin-bottom: 20px;">Daftar Menu</h2>
                <div class="grid">
                    <div v-for="p in products" :key="p.id" class="product-card">
                        <div style="font-size: 40px; margin-bottom: 10px;">{{ p.icon }}</div>
                        <h3 style="font-size: 1.1em;">{{ p.nama }}</h3>
                        <p style="color: var(--primary); font-weight: bold; margin: 5px 0;">Rp {{ p.harga.toLocaleString() }}</p>
                        <p><small>Stok: {{ p.stok }}</small></p>
                        <button class="btn btn-add" @click="addToCart(p)" :disabled="p.stok <= 0">
                            {{ p.stok > 0 ? 'Tambah' : 'Habis' }}
                        </button>
                    </div>
                </div>
            </div>

            <div class="checkout-panel">
                <h2 style="margin-bottom: 20px; border-bottom: 1px solid #444; padding-bottom: 10px;">Keranjang</h2>
                <div style="overflow-y: auto; flex-grow: 1;">
                    <div v-if="keranjang.length === 0" style="text-align: center; opacity: 0.5; margin-top: 50px;">
                        Belum ada item dipilih
                    </div>
                    <div v-for="(item, idx) in keranjang" :key="idx" class="cart-item">
                        <span>{{ item.nama }}</span>
                        <span>Rp {{ item.harga.toLocaleString() }}</span>
                    </div>
                </div>

                <div class="total-section">
                    <p style="font-size: 0.4em; margin: 0; opacity: 0.7; text-transform: uppercase;">Total Bayar</p>
                    Rp {{ total.toLocaleString() }}
                </div>

                <button v-if="total > 0" class="btn btn-pay" @click="showQRIS = true">BAYAR DENGAN QRIS</button>
            </div>
        </div>
    </div>

    <div v-if="showQRIS" class="overlay">
        <div class="qris-modal">
            <img src="https://upload.wikimedia.org/wikipedia/commons/a/a2/Logo_QRIS.svg" class="qris-logo">
            <p style="font-weight: bold; margin-bottom: 10px;">Scan QR Code</p>
            <img :src="'https://api.qrserver.com/v1/create-qr-code/?size=250x250&data=POS-TRX-' + total" class="qr-image">
            <h2 style="color: var(--primary);">Rp {{ total.toLocaleString() }}</h2>
            <p style="font-size: 0.8em; margin: 10px 0;">ID Transaksi: #{{ Math.floor(Date.now() / 1000) }}</p>
            
            <button class="btn" style="background: var(--primary); color: white;" @click="confirmPayment">KONFIRMASI LUNAS</button>
            <button class="btn" style="background: #eee; color: #333; margin-top: 10px;" @click="showQRIS = false">BATAL</button>
        </div>
    </div>
</div>

<script>
    const { createApp, ref, computed } = Vue;

    createApp({
        setup() {
            // State Halaman
            const page = ref('login');
            const username = ref('');
            const password = ref('');
            const showQRIS = ref(false);

            // Data Produk
            const products = ref([
                { id: 1, nama: 'Kopi Kenangan', harga: 18000, stok: 15, icon: '☕' },
                { id: 2, nama: 'Burger Spesial', harga: 35000, stok: 8, icon: '🍔' },
                { id: 3, nama: 'Kentang Goreng', harga: 15000, stok: 20, icon: '🍟' },
                { id: 4, nama: 'Es Teh Manis', harga: 5000, stok: 50, icon: '🍹' },
                { id: 5, nama: 'Roti Bakar', harga: 12000, stok: 10, icon: '🍞' }
            ]);

            const keranjang = ref([]);

            // Logika Login
            const doLogin = () => {
                if(username.value && password.value) {
                    page.value = 'dashboard';
                } else {
                    alert('Harap masukkan Username dan Password!');
                }
            };

            // Logika Keranjang
            const addToCart = (produk) => {
                keranjang.value.push({...produk});
                produk.stok--;
            };

            const total = computed(() => {
                return keranjang.value.reduce((sum, item) => sum + item.harga, 0);
            });

            // Logika Pembayaran
            const confirmPayment = () => {
                alert("Pembayaran Rp " + total.value.toLocaleString() + " Berhasil!");
                keranjang.value = [];
                showQRIS.value = false;
                page.value = 'dashboard'; // Kembali ke dashboard setelah lunas
            };

            return { 
                page, username, password, showQRIS, 
                products, keranjang, total, 
                doLogin, addToCart, confirmPayment 
            };
        }
    }).mount('#app');
</script>

</body>
</html>
