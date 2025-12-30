<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>كباب الديرة | إدارة الطلبات المتطورة</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Tajawal', sans-serif; background-color: #f3f4f6; color: #1f2937; }
        .glass-effect { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); border-bottom: 1px solid rgba(255,255,255,0.3); }
        .product-card { transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .product-card:hover { transform: translateY(-5px); box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); }
        .img-container { position: relative; overflow: hidden; border-radius: 1rem; aspect-ratio: 1/1; }
        .img-container img { transition: transform 0.5s ease; width: 100%; height: 100%; object-fit: cover; }
        .product-card:hover img { transform: scale(1.1); }
        .cart-badge { animation: bounce 0.5s ease; }
        @keyframes bounce { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.2); } }
        /* Mobile adjustments */
        @media (max-width: 640px) {
            .product-grid { grid-template-columns: repeat(2, 1fr); gap: 0.75rem; }
            .product-card h3 { font-size: 0.95rem; }
        }
    </style>
</head>
<body class="pb-24">

    <!-- Audio for Notifications -->
    <audio id="alertSound" src="https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3" preload="auto"></audio>

    <!-- Header -->
    <header class="glass-effect sticky top-0 z-50 p-4 flex justify-between items-center shadow-sm px-6">
        <h1 class="text-2xl font-black text-orange-600 tracking-tighter">كباب الديرة</h1>
        <div class="flex items-center gap-3">
            <button onclick="toggleDashboard()" class="bg-gray-100 p-2.5 rounded-full text-gray-600 hover:bg-orange-100 transition-colors">
                <i class="fas fa-chart-line text-lg"></i>
            </button>
            <button onclick="openCart()" class="relative bg-orange-600 p-2.5 rounded-full text-white shadow-lg shadow-orange-200">
                <i class="fas fa-shopping-basket text-lg"></i>
                <span id="cartCount" class="absolute -top-1 -right-1 bg-red-500 text-white text-[10px] font-bold rounded-full w-5 h-5 flex items-center justify-center border-2 border-white">0</span>
            </button>
        </div>
    </header>

    <!-- Main View -->
    <main id="appContent" class="max-w-5xl mx-auto p-4 md:p-8">
        <!-- Home View -->
        <section id="homeView">
            <div class="mb-8 flex justify-between items-end">
                <div>
                    <h2 class="text-3xl font-bold text-gray-800">قائمتنا المميزة</h2>
                    <p class="text-gray-500 mt-1">طعم الكباب الأصيل من الديرة إلى مائدتك</p>
                </div>
            </div>
            <div id="productList" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                <!-- Products injected here -->
            </div>
        </section>

        <!-- Dashboard View -->
        <section id="dashboardView" class="hidden">
            <div id="authPanel" class="max-w-md mx-auto text-center py-20 bg-white rounded-3xl shadow-xl p-8 mt-10">
                <div class="bg-orange-100 w-20 h-20 rounded-full flex items-center justify-center mx-auto mb-6">
                    <i class="fas fa-shield-alt text-3xl text-orange-600"></i>
                </div>
                <h2 class="text-2xl font-bold mb-2">منطقة الإدارة</h2>
                <p class="text-gray-500 mb-6">الرجاء إدخال رمز الحماية للوصول</p>
                <input type="password" id="passCode" placeholder="••••" class="w-full p-4 border-2 border-gray-100 rounded-2xl text-center text-2xl tracking-widest mb-4 focus:border-orange-500 outline-none transition-all">
                <button onclick="checkAuth()" class="w-full bg-orange-600 text-white p-4 rounded-2xl font-bold shadow-lg hover:bg-orange-700 transition-all">دخول النظام</button>
            </div>

            <div id="adminPanel" class="hidden space-y-8">
                <div class="flex justify-between items-center bg-white p-6 rounded-3xl shadow-sm border border-gray-100">
                    <div>
                        <h2 class="text-2xl font-bold">لوحة الإدارة</h2>
                        <p class="text-sm text-gray-500">تحكم بمتجرك بكفاءة</p>
                    </div>
                    <button onclick="logout()" class="bg-red-50 p-3 rounded-xl text-red-600 font-bold hover:bg-red-100 transition-all">
                        <i class="fas fa-sign-out-alt ml-2"></i>خروج
                    </button>
                </div>

                <!-- Stats Dashboard -->
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="bg-white p-6 rounded-3xl border shadow-sm flex flex-col items-center">
                        <div class="text-blue-500 mb-2"><i class="fas fa-receipt text-2xl"></i></div>
                        <p class="text-xs text-gray-400 font-medium uppercase tracking-wider">الطلبات</p>
                        <p id="totalOrders" class="text-2xl font-bold mt-1">0</p>
                    </div>
                    <div class="bg-white p-6 rounded-3xl border shadow-sm flex flex-col items-center">
                        <div class="text-green-500 mb-2"><i class="fas fa-wallet text-2xl"></i></div>
                        <p class="text-xs text-gray-400 font-medium uppercase tracking-wider">المبيعات</p>
                        <p id="totalRevenue" class="text-2xl font-bold mt-1">0</p>
                    </div>
                    <div class="bg-white p-6 rounded-3xl border shadow-sm flex flex-col items-center">
                        <div class="text-orange-500 mb-2"><i class="fas fa-utensils text-2xl"></i></div>
                        <p class="text-xs text-gray-400 font-medium uppercase tracking-wider">المنتجات</p>
                        <p id="totalProdCount" class="text-2xl font-bold mt-1">0</p>
                    </div>
                    <div class="bg-white p-6 rounded-3xl border shadow-sm flex flex-col items-center">
                        <div class="text-purple-500 mb-2"><i class="fas fa-users text-2xl"></i></div>
                        <p class="text-xs text-gray-400 font-medium uppercase tracking-wider">الزبائن</p>
                        <p id="totalCustomers" class="text-2xl font-bold mt-1">0</p>
                    </div>
                </div>

                <!-- Actions -->
                <div class="bg-white p-6 rounded-3xl shadow-sm border flex flex-col md:flex-row gap-4">
                    <button onclick="showProductModal()" class="flex-1 bg-blue-600 text-white p-4 rounded-2xl font-bold flex items-center justify-center gap-3 shadow-blue-100 shadow-xl hover:bg-blue-700 transition-all">
                        <i class="fas fa-plus-circle text-xl"></i> إضافة منتج جديد بالصور
                    </button>
                </div>

                <!-- Admin Tabs -->
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                    <div>
                        <h3 class="text-xl font-bold mb-4 px-2">سجل الفواتير الحديثة</h3>
                        <div id="invoiceHistory" class="space-y-4 max-h-[600px] overflow-y-auto pr-2">
                            <!-- Invoices here -->
                        </div>
                    </div>
                    <div>
                        <h3 class="text-xl font-bold mb-4 px-2">إدارة القائمة</h3>
                        <div id="adminProductList" class="space-y-4 max-h-[600px] overflow-y-auto pr-2">
                            <!-- Admin Products here -->
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- Product Modal (Add/Edit) -->
    <div id="productModal" class="hidden fixed inset-0 bg-black/60 z-[100] flex items-end sm:items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white w-full max-w-xl rounded-t-[2.5rem] sm:rounded-[2.5rem] p-8 overflow-y-auto max-h-[90vh] shadow-2xl transition-all">
            <div class="flex justify-between items-center mb-6">
                <h3 id="modalTitle" class="text-2xl font-bold">منتج جديد</h3>
                <button onclick="closeProductModal()" class="text-gray-400 hover:text-black transition-colors"><i class="fas fa-times text-xl"></i></button>
            </div>
            
            <input type="hidden" id="editingId">
            
            <div class="space-y-6">
                <!-- Image Upload -->
                <div class="flex flex-col items-center">
                    <div id="imagePreviewContainer" onclick="document.getElementById('imgInput').click()" class="w-40 h-40 bg-gray-50 border-2 border-dashed border-gray-200 rounded-3xl flex flex-col items-center justify-center cursor-pointer overflow-hidden hover:border-orange-400 transition-all">
                        <img id="imgPreview" src="" class="hidden w-full h-full object-cover">
                        <div id="imgPlaceholder" class="text-center p-4">
                            <i class="fas fa-camera text-3xl text-gray-300 mb-2"></i>
                            <p class="text-[10px] text-gray-400">إضافة صورة المنتج</p>
                        </div>
                    </div>
                    <input type="file" id="imgInput" accept="image/*" class="hidden" onchange="previewImage(this)">
                    <button id="removeImgBtn" onclick="removeSelectedImage()" class="hidden text-red-500 text-xs mt-2 font-bold underline">حذف الصورة</button>
                </div>

                <div>
                    <label class="block text-sm font-bold text-gray-600 mb-2">اسم المنتج</label>
                    <input type="text" id="pName" placeholder="مثال: كباب لحم غنم" class="w-full p-4 bg-gray-50 border-0 rounded-2xl focus:ring-2 focus:ring-orange-500 outline-none">
                </div>

                <div id="variantsContainer" class="space-y-4 border-t pt-4">
                    <div class="flex justify-between items-center">
                        <label class="block text-sm font-bold text-gray-600">المتغيرات والأسعار</label>
                        <button onclick="addVariantRow()" class="text-blue-600 text-xs font-bold">+ إضافة خيار آخر</button>
                    </div>
                    <div id="variantsList" class="space-y-3">
                        <!-- Variant rows injected here -->
                    </div>
                </div>

                <div class="flex gap-4 pt-4">
                    <button onclick="saveProduct()" class="flex-[2] bg-orange-600 text-white p-4 rounded-2xl font-bold shadow-lg shadow-orange-100 hover:bg-orange-700 transition-all">حفظ المنتج</button>
                    <button onclick="closeProductModal()" class="flex-1 bg-gray-100 text-gray-700 p-4 rounded-2xl font-bold hover:bg-gray-200 transition-all">إلغاء</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Cart Overlay -->
    <div id="cartOverlay" class="hidden fixed inset-0 bg-black/60 z-[60] flex justify-end backdrop-blur-sm">
        <div class="bg-white w-full max-w-md h-full shadow-2xl flex flex-col animate-slide-left">
            <div class="p-6 border-b flex justify-between items-center bg-orange-50">
                <div>
                    <h3 class="font-bold text-xl">سلة التسوق</h3>
                    <p id="cartItemsCount" class="text-xs text-orange-600 font-medium mt-1">0 أصناف مختارة</p>
                </div>
                <button onclick="closeCart()" class="bg-white p-3 rounded-full shadow-sm text-gray-400 hover:text-black"><i class="fas fa-times"></i></button>
            </div>
            
            <div id="cartItems" class="flex-1 overflow-y-auto p-6 space-y-4">
                <!-- Cart items here -->
            </div>
            
            <div class="p-6 border-t bg-white space-y-6">
                <div class="grid grid-cols-2 gap-3">
                    <div class="col-span-2">
                        <label class="block text-[10px] font-bold text-gray-400 mb-1 mr-2 uppercase">بيانات التوصيل</label>
                        <input type="text" id="custName" placeholder="الاسم الكامل" class="w-full p-4 bg-gray-50 border-0 rounded-2xl focus:ring-2 focus:ring-orange-500 outline-none text-sm">
                    </div>
                    <input type="tel" id="custPhone" placeholder="رقم الهاتف" class="w-full p-4 bg-gray-50 border-0 rounded-2xl focus:ring-2 focus:ring-orange-500 outline-none text-sm">
                    <input type="text" id="custAddress" placeholder="المنطقة/العنوان" class="w-full p-4 bg-gray-50 border-0 rounded-2xl focus:ring-2 focus:ring-orange-500 outline-none text-sm">
                </div>
                
                <div class="flex justify-between items-center bg-gray-900 text-white p-5 rounded-3xl">
                    <span class="text-gray-400 font-medium">المبلغ الإجمالي</span>
                    <span id="cartTotal" class="text-2xl font-black">0 د.ع</span>
                </div>
                
                <button onclick="checkout()" class="w-full bg-green-600 text-white p-5 rounded-3xl font-bold flex items-center justify-center gap-3 shadow-xl shadow-green-100 hover:bg-green-700 active:scale-95 transition-all">
                    <i class="fab fa-whatsapp text-2xl"></i> إتمام الطلب عبر واتساب
                </button>
            </div>
        </div>
    </div>

    <script>
        let products = JSON.parse(localStorage.getItem('products')) || [];
        let cart = [];
        let invoices = JSON.parse(localStorage.getItem('invoices')) || [];
        let isAdmin = false;
        let selectedImageData = "";

        // --- Core Functions ---

        function previewImage(input) {
            if (input.files && input.files[0]) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    selectedImageData = e.target.result;
                    document.getElementById('imgPreview').src = selectedImageData;
                    document.getElementById('imgPreview').classList.remove('hidden');
                    document.getElementById('imgPlaceholder').classList.add('hidden');
                    document.getElementById('removeImgBtn').classList.remove('hidden');
                }
                reader.readAsDataURL(input.files[0]);
            }
        }

        function removeSelectedImage() {
            selectedImageData = "";
            document.getElementById('imgPreview').src = "";
            document.getElementById('imgPreview').classList.add('hidden');
            document.getElementById('imgPlaceholder').classList.remove('hidden');
            document.getElementById('removeImgBtn').classList.add('hidden');
            document.getElementById('imgInput').value = "";
        }

        function checkAuth() {
            const pin = document.getElementById('passCode').value;
            if (pin === '1234') {
                isAdmin = true;
                document.getElementById('authPanel').classList.add('hidden');
                document.getElementById('adminPanel').classList.remove('hidden');
                renderAdmin();
            } else {
                alert('الرمز خاطئ!');
            }
        }

        function logout() {
            isAdmin = false;
            document.getElementById('authPanel').classList.remove('hidden');
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('passCode').value = '';
        }

        function toggleDashboard() {
            const dash = document.getElementById('dashboardView');
            const home = document.getElementById('homeView');
            if(dash.classList.contains('hidden')) {
                dash.classList.remove('hidden');
                home.classList.add('hidden');
                if(isAdmin) renderAdmin();
            } else {
                dash.classList.add('hidden');
                home.classList.remove('hidden');
            }
        }

        // --- Product Management ---

        function showProductModal(editId = null) {
            const modal = document.getElementById('productModal');
            const title = document.getElementById('modalTitle');
            const nameInput = document.getElementById('pName');
            const idInput = document.getElementById('editingId');
            const variantsList = document.getElementById('variantsList');

            variantsList.innerHTML = '';
            removeSelectedImage();

            if(editId) {
                const prod = products.find(p => p.id == editId);
                title.innerText = 'تعديل المنتج';
                nameInput.value = prod.name;
                idInput.value = prod.id;
                if(prod.image) {
                    selectedImageData = prod.image;
                    document.getElementById('imgPreview').src = selectedImageData;
                    document.getElementById('imgPreview').classList.remove('hidden');
                    document.getElementById('imgPlaceholder').classList.add('hidden');
                    document.getElementById('removeImgBtn').classList.remove('hidden');
                }
                prod.variants.forEach(v => addVariantRow(v.vName, v.vCost, v.vPrice));
            } else {
                title.innerText = 'منتج جديد';
                nameInput.value = '';
                idInput.value = '';
                addVariantRow();
            }
            modal.classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('productModal').classList.add('hidden');
        }

        function addVariantRow(name = '', cost = '', price = '') {
            const div = document.createElement('div');
            div.className = 'variant-row flex gap-2 items-center animate-fade-in';
            div.innerHTML = `
                <input type="text" placeholder="الاسم" value="${name}" class="flex-1 p-3 bg-gray-50 rounded-xl text-sm v-name outline-none border border-transparent focus:border-orange-400">
                <input type="number" placeholder="تكلفة" value="${cost}" class="w-20 p-3 bg-gray-50 rounded-xl text-sm v-cost outline-none border border-transparent focus:border-orange-400">
                <input type="number" placeholder="سعر" value="${price}" class="w-24 p-3 bg-gray-100 rounded-xl text-sm v-price outline-none font-bold text-orange-600 border border-transparent focus:border-orange-400">
                <button onclick="this.parentElement.remove()" class="text-gray-300 hover:text-red-500"><i class="fas fa-times-circle"></i></button>
            `;
            document.getElementById('variantsList').appendChild(div);
        }

        function saveProduct() {
            const name = document.getElementById('pName').value;
            const editId = document.getElementById('editingId').value;
            const rows = document.querySelectorAll('.variant-row');
            let variants = [];
            
            rows.forEach(row => {
                const vName = row.querySelector('.v-name').value;
                const vCost = row.querySelector('.v-cost').value;
                const vPrice = row.querySelector('.v-price').value;
                if(vName && vPrice) {
                    variants.push({ vName, vCost: parseFloat(vCost) || 0, vPrice: parseFloat(vPrice) });
                }
            });

            if(!name || variants.length === 0) return alert('يرجى كتابة اسم المنتج وسعر واحد على الأقل');

            if(editId) {
                const index = products.findIndex(p => p.id == editId);
                products[index] = { ...products[index], name, variants, image: selectedImageData };
            } else {
                products.push({ id: Date.now(), name, variants, image: selectedImageData });
            }

            localStorage.setItem('products', JSON.stringify(products));
            renderProducts();
            if(isAdmin) renderAdmin();
            closeProductModal();
        }

        function deleteProduct(id) {
            if(confirm('هل أنت متأكد من حذف هذا المنتج نهائياً؟')) {
                products = products.filter(p => p.id != id);
                localStorage.setItem('products', JSON.stringify(products));
                renderProducts();
                renderAdmin();
            }
        }

        // --- UI Rendering ---

        function renderProducts() {
            const list = document.getElementById('productList');
            list.innerHTML = products.length ? '' : `
                <div class="col-span-full py-20 text-center">
                    <div class="bg-white w-24 h-24 rounded-full flex items-center justify-center mx-auto mb-4 text-gray-200 shadow-inner">
                        <i class="fas fa-utensils text-4xl"></i>
                    </div>
                    <p class="text-gray-400 font-medium">القائمة فارغة حالياً</p>
                </div>
            `;
            
            products.forEach(p => {
                const card = document.createElement('div');
                card.className = 'bg-white rounded-[2rem] shadow-sm border border-gray-100 p-3 product-card flex flex-col';
                card.innerHTML = `
                    <div class="img-container mb-4 shadow-sm bg-gray-50">
                        <img src="${p.image || 'https://via.placeholder.com/400x400?text=No+Image'}" alt="${p.name}">
                        <div class="absolute top-3 left-3 bg-white/90 px-3 py-1 rounded-full text-[10px] font-bold text-orange-600 backdrop-blur-sm">
                            طازج يومياً
                        </div>
                    </div>
                    <div class="px-2 flex-1">
                        <h3 class="font-bold text-lg text-gray-800 mb-4 line-clamp-1">${p.name}</h3>
                        <div class="flex flex-col gap-2">
                            ${p.variants.map(v => `
                                <button onclick="addToCart(${p.id}, '${p.name}', '${v.vName}', ${v.vPrice})" 
                                        class="w-full bg-gray-50 hover:bg-orange-50 text-gray-700 hover:text-orange-700 p-3 rounded-2xl text-xs font-bold transition-all flex justify-between items-center group">
                                    <span class="flex items-center gap-2"><i class="fas fa-plus-circle text-gray-300 group-hover:text-orange-500 transition-colors"></i> ${v.vName}</span>
                                    <span class="bg-white px-2 py-1 rounded-lg border border-gray-100">${v.vPrice} د.ع</span>
                                </button>
                            `).join('')}
                        </div>
                    </div>
                `;
                list.appendChild(card);
            });
        }

        // --- Cart Functions ---

        function addToCart(pId, pName, vName, price) {
            cart.push({ pId, pName, vName, price });
            updateCartUI();
            
            // Visual feedback
            const btn = document.querySelector(`button[onclick*="${pId}"][onclick*="${vName}"]`);
            if(btn) {
                const originalText = btn.innerHTML;
                btn.classList.add('bg-green-50', 'text-green-700');
                btn.innerHTML = 'تمت الإضافة ✓';
                setTimeout(() => {
                    btn.classList.remove('bg-green-50', 'text-green-700');
                    btn.innerHTML = originalText;
                }, 800);
            }
        }

        function updateCartUI() {
            const countEl = document.getElementById('cartCount');
            countEl.innerText = cart.length;
            countEl.classList.remove('cart-badge');
            void countEl.offsetWidth; // trigger reflow
            countEl.classList.add('cart-badge');

            document.getElementById('cartItemsCount').innerText = `${cart.length} أصناف مختارة`;

            const itemsCont = document.getElementById('cartItems');
            itemsCont.innerHTML = '';
            let total = 0;
            
            cart.forEach((item, index) => {
                total += item.price;
                const div = document.createElement('div');
                div.className = 'flex gap-4 items-center bg-gray-50 p-4 rounded-3xl animate-fade-in';
                div.innerHTML = `
                    <div class="flex-1">
                        <p class="font-bold text-sm text-gray-800">${item.pName}</p>
                        <p class="text-[11px] text-gray-400 font-bold uppercase tracking-wider">${item.vName}</p>
                    </div>
                    <div class="flex items-center gap-4">
                        <span class="font-black text-orange-600">${item.price}</span>
                        <button onclick="removeFromCart(${index})" class="w-10 h-10 bg-white rounded-full text-red-400 shadow-sm border border-gray-100 hover:text-red-600 hover:bg-red-50 transition-all"><i class="fas fa-times text-xs"></i></button>
                    </div>
                `;
                itemsCont.appendChild(div);
            });
            document.getElementById('cartTotal').innerText = total.toLocaleString() + ' د.ع';
        }

        function removeFromCart(index) {
            cart.splice(index, 1);
            updateCartUI();
        }

        function openCart() { 
            document.getElementById('cartOverlay').classList.remove('hidden'); 
            document.body.style.overflow = 'hidden';
        }
        function closeCart() { 
            document.getElementById('cartOverlay').classList.add('hidden'); 
            document.body.style.overflow = 'auto';
        }

        // --- Checkout Logic ---

        function playAlert() {
            const sound = document.getElementById('alertSound');
            sound.volume = 1.0;
            sound.play().catch(() => {});
        }

        function checkout() {
            const name = document.getElementById('custName').value;
            const phone = document.getElementById('custPhone').value;
            const address = document.getElementById('custAddress').value;

            if(!name || !phone || !address || cart.length === 0) return alert('الرجاء تعبئة بيانات الزبون واضافة منتجات');

            let total = 0;
            let orderText = `*طلبية جديدة من كباب الديرة*%0A`;
            orderText += `-----------------------%0A`;
            orderText += `*الاسم:* ${name}%0A`;
            orderText += `*الهاتف:* ${phone}%0A`;
            orderText += `*العنوان:* ${address}%0A`;
            orderText += `-----------------------%0A`;
            
            cart.forEach(item => {
                orderText += `• ${item.pName} (${item.vName}) - ${item.price} د.ع%0A`;
                total += item.price;
            });

            orderText += `-----------------------%0A`;
            orderText += `*المجموع الكلي: ${total.toLocaleString()} د.ع*`;

            // Save to Invoices
            const newInvoice = {
                id: Date.now(),
                customer: name,
                phone: phone,
                address: address,
                items: [...cart],
                total: total,
                date: new Date().toLocaleString('ar-EG')
            };
            invoices.push(newInvoice);
            localStorage.setItem('invoices', JSON.stringify(invoices));

            playAlert();

            const waNumber = "964770000000"; 
            window.open(`https://wa.me/${waNumber}?text=${orderText}`, '_blank');
            
            cart = [];
            updateCartUI();
            closeCart();
            renderAdmin();
        }

        // --- Admin Rendering ---

        function renderAdmin() {
            if(!isAdmin) return;
            
            const uniqueCustomers = new Set(invoices.map(inv => inv.phone)).size;
            const totalRevenue = invoices.reduce((acc, inv) => acc + inv.total, 0);
            
            document.getElementById('totalOrders').innerText = invoices.length;
            document.getElementById('totalRevenue').innerText = totalRevenue.toLocaleString() + ' د.ع';
            document.getElementById('totalProdCount').innerText = products.length;
            document.getElementById('totalCustomers').innerText = uniqueCustomers;

            // Render Invoices
            const historyCont = document.getElementById('invoiceHistory');
            historyCont.innerHTML = invoices.length ? '' : '<p class="text-gray-400 text-center py-10 bg-white rounded-3xl">لا توجد مبيعات مسجلة</p>';

            [...invoices].reverse().slice(0, 20).forEach(inv => {
                const div = document.createElement('div');
                div.className = 'bg-white p-6 rounded-3xl border border-gray-50 shadow-sm transition-all hover:border-orange-200';
                div.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <div>
                            <span class="bg-gray-900 text-white text-[10px] px-2 py-1 rounded-md">ID #${inv.id.toString().slice(-4)}</span>
                            <p class="font-black text-gray-800 mt-2">${inv.customer}</p>
                            <p class="text-xs text-gray-400 mt-1"><i class="fas fa-map-marker-alt ml-1"></i>${inv.address}</p>
                        </div>
                        <span class="text-[10px] text-gray-400 font-bold">${inv.date}</span>
                    </div>
                    <div class="space-y-1 mb-4 border-y py-3 border-gray-50">
                        ${inv.items.map(i => `<div class="flex justify-between text-xs"><span class="text-gray-500">${i.pName} (${i.vName})</span> <span class="font-bold">${i.price}</span></div>`).join('')}
                    </div>
                    <div class="flex justify-between items-center">
                        <span class="text-xs font-bold text-gray-400">إجمالي المدفوع</span>
                        <span class="text-lg font-black text-orange-600">${inv.total.toLocaleString()} د.ع</span>
                    </div>
                `;
                historyCont.appendChild(div);
            });

            // Render Manageable Product List
            const adminProdCont = document.getElementById('adminProductList');
            adminProdCont.innerHTML = '';
            products.forEach(p => {
                const div = document.createElement('div');
                div.className = 'bg-white p-3 rounded-2xl border flex items-center gap-4 hover:shadow-md transition-all';
                div.innerHTML = `
                    <img src="${p.image || 'https://via.placeholder.com/100'}" class="w-14 h-14 rounded-xl object-cover">
                    <div class="flex-1">
                        <p class="font-bold text-sm">${p.name}</p>
                        <p class="text-[10px] text-gray-400">${p.variants.length} خيارات أسعار</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="showProductModal(${p.id})" class="w-9 h-9 bg-blue-50 text-blue-600 rounded-xl hover:bg-blue-100 transition-colors"><i class="fas fa-edit text-xs"></i></button>
                        <button onclick="deleteProduct(${p.id})" class="w-9 h-9 bg-red-50 text-red-600 rounded-xl hover:bg-red-100 transition-colors"><i class="fas fa-trash-alt text-xs"></i></button>
                    </div>
                `;
                adminProdCont.appendChild(div);
            });
        }

        // --- Init ---
        window.onload = () => {
            renderProducts();
        };

    </script>
</body>
</html>
