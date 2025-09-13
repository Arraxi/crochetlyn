<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CrochetLyn</title>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Dancing+Script:wght@400..700&family=Inter:wght@100..900&display=swap" rel="stylesheet">
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f3f0; /* Soft cream/beige background */
            color: #4a4a4a;
        }
        .header-font {
            font-family: 'Dancing Script', cursive;
        }
        .text-rose {
            color: #d1b4b4;
        }
        .bg-rose {
            background-color: #d1b4b4;
        }
        .border-rose {
            border-color: #d1b4b4;
        }
        .hover\:bg-rose-dark:hover {
            background-color: #c09e9e;
        }
        .scrollbar-hide::-webkit-scrollbar {
            display: none;
        }
        .scrollbar-hide {
            -ms-overflow-style: none; /* IE and Edge */
            scrollbar-width: none; /* Firefox */
        }
    </style>
</head>
<body class="flex flex-col h-screen overflow-hidden">
    <!-- Banner -->
    <header class="flex-shrink-0 bg-stone-100 p-6 md:p-8 flex items-center justify-between shadow-sm">
        <div class="flex items-center">
            <h1 class="header-font text-4xl sm:text-5xl md:text-6xl text-gray-800">CrochetLyn</h1>
        </div>
        <div class="flex items-center space-x-4">
            <div id="notification-bell" class="relative hidden">
                <button class="p-2 rounded-full hover:bg-gray-200 transition-colors duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-bell"><path d="M6 8a6 6 0 0 1 12 0c0 7-3 9-3 9H9s-3-2-3-9"/><path d="M10.375 22a2 2 0 1 0 3.25 0"/></svg>
                </button>
                <span id="notification-count" class="absolute -top-1 -right-1 bg-red-500 text-white text-xs font-bold rounded-full h-5 w-5 flex items-center justify-center">0</span>
            </div>
            <button id="admin-login-btn" class="px-4 py-2 bg-rose text-white rounded-lg shadow hover:bg-rose-dark transition-colors duration-200">Admin Login</button>
            <button id="admin-toggle" class="px-4 py-2 bg-rose text-white rounded-lg shadow hover:bg-rose-dark transition-colors duration-200 hidden">Admin Mode</button>
        </div>
    </header>
    
    <!-- Main Container -->
    <div class="flex flex-1 flex-col md:flex-row">
        <!-- Sidebar (for desktop) -->
        <aside class="hidden md:flex flex-col bg-white p-6 shadow-xl w-64 flex-shrink-0">
            <nav class="space-y-4 flex-1 mt-8">
                <button id="nav-purchase" class="w-full text-left p-4 rounded-lg transition-colors duration-200 hover:bg-rose hover:text-white">
                    <span class="font-semibold">Purchase</span>
                </button>
                <button id="nav-order" class="w-full text-left p-4 rounded-lg transition-colors duration-200 hover:bg-rose hover:text-white">
                    <span class="font-semibold">Order</span>
                </button>
                <button id="nav-custom" class="w-full text-left p-4 rounded-lg transition-colors duration-200 hover:bg-rose hover:text-white">
                    <span class="font-semibold">Customized Order</span>
                </button>
            </nav>
        </aside>

        <!-- Main Content Area -->
        <main id="main-content" class="flex-1 p-6 md:p-12 overflow-y-auto scrollbar-hide">
            <!-- Content will be rendered here by JavaScript -->
        </main>
    </div>

    <!-- Modal for Messages -->
    <div id="modal-backdrop" class="fixed inset-0 bg-gray-900 bg-opacity-50 hidden items-center justify-center p-4 z-50">
        <div class="bg-white p-8 rounded-lg shadow-xl max-w-sm w-full">
            <h3 id="modal-title" class="text-xl font-bold mb-4 text-gray-800"></h3>
            <p id="modal-message" class="text-gray-600 mb-6"></p>
            <div class="flex justify-end space-x-4">
                <button id="modal-close" class="px-6 py-2 bg-gray-200 text-gray-700 rounded-lg transition-colors duration-200 hover:bg-gray-300">Close</button>
            </div>
        </div>
    </div>

    <!-- Password Modal -->
    <div id="password-modal" class="fixed inset-0 bg-gray-900 bg-opacity-50 hidden items-center justify-center p-4 z-50">
        <div class="bg-white p-8 rounded-lg shadow-xl max-w-sm w-full">
            <h3 class="text-xl font-bold mb-4 text-gray-800">Enter Admin Password</h3>
            <input type="password" id="password-input" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose" placeholder="Password">
            <p id="password-error" class="text-red-500 text-sm mt-2 hidden">Incorrect password. Please try again.</p>
            <div class="flex justify-end space-x-4 mt-6">
                <button id="password-cancel-btn" class="px-6 py-2 bg-gray-200 text-gray-700 rounded-lg transition-colors duration-200 hover:bg-gray-300">Cancel</button>
                <button id="password-submit-btn" class="px-6 py-2 bg-rose text-white rounded-lg shadow hover:bg-rose-dark transition-colors duration-200">Submit</button>
            </div>
        </div>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, addDoc, setDoc, updateDoc, deleteDoc, onSnapshot, collection, query, where, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
        
        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);
        
        let userId = null;
        let isAdmin = false;
        let currentView = 'home';
        let customOrders = [];
        let purchaseProducts = [];
        let orderProducts = [];

        // UI Elements
        const mainContent = document.getElementById('main-content');
        const navButtons = {
            purchase: document.getElementById('nav-purchase'),
            order: document.getElementById('nav-order'),
            custom: document.getElementById('nav-custom'),
        };
        const adminLoginBtn = document.getElementById('admin-login-btn');
        const adminToggleBtn = document.getElementById('admin-toggle');
        const notificationBell = document.getElementById('notification-bell');
        const notificationCountSpan = document.getElementById('notification-count');
        const modalBackdrop = document.getElementById('modal-backdrop');
        const modalTitle = document.getElementById('modal-title');
        const modalMessage = document.getElementById('modal-message');
        const modalCloseBtn = document.getElementById('modal-close');
        
        // Password Modal Elements
        const passwordModal = document.getElementById('password-modal');
        const passwordInput = document.getElementById('password-input');
        const passwordError = document.getElementById('password-error');
        const passwordSubmitBtn = document.getElementById('password-submit-btn');
        const passwordCancelBtn = document.getElementById('password-cancel-btn');

        // Initial Authentication
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                userId = user.uid;
                console.log("Authenticated as:", userId);
                await initializeListeners();
            } else {
                console.log("No user, signing in...");
                try {
                    if (initialAuthToken) {
                        await signInWithCustomToken(auth, initialAuthToken);
                    } else {
                        await signInAnonymously(auth);
                    }
                } catch (error) {
                    console.error("Error during authentication:", error);
                }
            }
        });

        async function initializeListeners() {
            if (!userId) {
                console.error("User ID is not set. Cannot initialize listeners.");
                return;
            }

            // Public Collection for Custom Orders
            const customOrdersColRef = collection(db, `artifacts/${appId}/public/data/custom_orders`);
            onSnapshot(customOrdersColRef, (snapshot) => {
                customOrders = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                updateNotificationCount();
                renderView();
            });

            // Private Collection for Admin's Products
            const purchaseProductsColRef = collection(db, `artifacts/${appId}/users/${userId}/purchase_products`);
            onSnapshot(purchaseProductsColRef, (snapshot) => {
                purchaseProducts = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderView();
            });

            const orderProductsColRef = collection(db, `artifacts/${appId}/users/${userId}/order_products`);
            onSnapshot(orderProductsColRef, (snapshot) => {
                orderProducts = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderView();
            });

            renderView(); // Initial render
        }

        function showModal(title, message) {
            modalTitle.textContent = title;
            modalMessage.textContent = message;
            modalBackdrop.classList.remove('hidden');
            modalBackdrop.classList.add('flex');
        }

        modalCloseBtn.addEventListener('click', () => {
            modalBackdrop.classList.remove('flex');
            modalBackdrop.classList.add('hidden');
        });

        function updateNotificationCount() {
            const count = customOrders.length;
            notificationCountSpan.textContent = count;
            notificationBell.classList.toggle('hidden', !isAdmin);
        }

        function renderView() {
            // Check if Firebase is ready before rendering
            if (!userId) {
                mainContent.innerHTML = '<div class="text-center mt-20 text-gray-500">Loading...</div>';
                return;
            }
            mainContent.innerHTML = '';
            switch (currentView) {
                case 'home':
                    renderHome();
                    break;
                case 'purchase':
                    renderPurchase();
                    break;
                case 'order':
                    renderOrder();
                    break;
                case 'custom':
                    renderCustom();
                    break;
            }
            updateActiveNavButton();
        }

        function updateActiveNavButton() {
            Object.values(navButtons).forEach(btn => btn.classList.remove('bg-rose', 'text-white'));
            if (navButtons[currentView]) {
                navButtons[currentView].classList.add('bg-rose', 'text-white');
            }
        }

        function createProductCard(product) {
            return `
                <div class="bg-white rounded-xl shadow-lg p-6 flex flex-col items-center text-center">
                    <img src="${product.imageUrl}" alt="${product.name}" class="w-full h-48 object-cover rounded-lg mb-4 border border-gray-200">
                    <h4 class="font-bold text-lg mb-2 text-gray-800">${product.name}</h4>
                    <p class="text-rose font-semibold mb-2">$${product.price.toFixed(2)}</p>
                    <p class="text-sm text-gray-500 mb-4">${product.available ? 'In Stock' : 'Out of Stock'}</p>
                    <button class="w-full px-4 py-2 bg-gray-800 text-white rounded-lg shadow-md hover:bg-gray-700 transition-colors duration-200">
                        ${product.available ? 'Add to Cart' : 'Notify Me'}
                    </button>
                </div>
            `;
        }
        
        // --- Render Functions ---
        function renderHome() {
            const featuredProducts = [
                { name: "Delicate Shawl", price: 55.00, imageUrl: "https://placehold.co/400x300/F2F2F2/7B7B7B?text=Shawl" },
                { name: "Hand-knit Teddy", price: 30.00, imageUrl: "https://placehold.co/400x300/E8E8E8/6F6F6F?text=Teddy" },
                { name: "Cozy Blanket", price: 85.00, imageUrl: "https://placehold.co/400x300/F0F0F0/8E8E8E?text=Blanket" },
                { name: "Stylish Tote Bag", price: 45.00, imageUrl: "https://placehold.co/400x300/E5E5E5/9A9A9A?text=Tote" },
                { name: "Baby Hat", price: 20.00, imageUrl: "https://placehold.co/400x300/D9D9D9/A6A6A6?text=Hat" },
            ];

            mainContent.innerHTML = `
                <section class="mb-12">
                    <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Top-Selling Crafts</h2>
                    <div id="carousel-container" class="relative overflow-hidden w-full">
                        <div id="carousel" class="flex transition-transform duration-500 ease-in-out scrollbar-hide snap-x snap-mandatory overflow-x-auto">
                            ${featuredProducts.map(p => `
                                <div class="flex-shrink-0 w-full sm:w-1/2 md:w-1/3 lg:w-1/4 snap-start p-2">
                                    <div class="bg-white rounded-xl shadow-lg p-6 flex flex-col items-center text-center">
                                        <img src="${p.imageUrl}" alt="${p.name}" class="w-full h-48 object-cover rounded-lg mb-4 border border-gray-200">
                                        <h4 class="font-bold text-lg mb-2 text-gray-800">${p.name}</h4>
                                        <p class="text-rose font-semibold mb-4">$${p.price.toFixed(2)}</p>
                                        <button class="w-full px-4 py-2 bg-gray-800 text-white rounded-lg shadow-md hover:bg-gray-700 transition-colors duration-200">
                                            Shop Now
                                        </button>
                                    </div>
                                </div>
                            `).join('')}
                        </div>
                    </div>
                </section>
                <div class="text-center mt-16">
                    <h3 class="text-2xl font-bold text-gray-800 mb-4">Welcome to CrochetLyn</h3>
                    <p class="text-gray-600 max-w-2xl mx-auto">
                        Discover handcrafted elegance and warmth. From delicate shawls to cozy blankets, each piece is
                        meticulously crafted to bring a touch of artistry to your home. Explore our collections or
                        request a custom piece to match your unique style.
                    </p>
                </div>
            `;
        }

        function renderPurchase() {
            if (isAdmin) {
                renderAdminForm('purchase_products', purchaseProducts);
            } else {
                mainContent.innerHTML = `
                    <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Available Products</h2>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
                        ${purchaseProducts.map(createProductCard).join('')}
                    </div>
                    ${purchaseProducts.length === 0 ? '<p class="text-center text-gray-500 mt-10">No products available for purchase.</p>' : ''}
                `;
            }
        }

        function renderOrder() {
            if (isAdmin) {
                renderAdminForm('order_products', orderProducts);
            } else {
                mainContent.innerHTML = `
                    <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Designs Available for Order</h2>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
                        ${orderProducts.map(createProductCard).join('')}
                    </div>
                    ${orderProducts.length === 0 ? '<p class="text-center text-gray-500 mt-10">No designs available for order yet.</p>' : ''}
                `;
            }
        }

        function renderCustom() {
            if (isAdmin) {
                renderAdminCustomOrders();
            } else {
                mainContent.innerHTML = `
                    <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Request a Customized Order</h2>
                    <div class="max-w-xl mx-auto bg-white p-8 rounded-xl shadow-lg">
                        <form id="custom-order-form" class="space-y-6">
                            <div>
                                <label for="custom-images" class="block text-gray-700 font-semibold mb-2">Upload Reference Images (Max 3)</label>
                                <input type="file" id="custom-images" multiple accept="image/*" class="w-full text-gray-700">
                                <button type="button" id="analyze-image-btn" class="mt-2 w-full px-4 py-2 bg-gray-200 text-gray-700 rounded-lg shadow hover:bg-gray-300 transition-colors duration-200">
                                    Analyze Image ✨
                                </button>
                                <div id="image-preview-container" class="flex flex-wrap gap-4 mt-4"></div>
                            </div>
                            <div>
                                <label for="custom-description" class="block text-gray-700 font-semibold mb-2">Description</label>
                                <textarea id="custom-description" rows="4" placeholder="Describe the size, colors, and any specific details you want..." class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose"></textarea>
                            </div>
                            <div>
                                <label for="custom-price" class="block text-gray-700 font-semibold mb-2">Estimated Price Range (Optional)</label>
                                <input type="text" id="custom-price" placeholder="$50 - $75" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose">
                            </div>
                            <div>
                                <label for="custom-name" class="block text-gray-700 font-semibold mb-2">Your Name</label>
                                <input type="text" id="custom-name" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose">
                            </div>
                            <div>
                                <label for="custom-phone" class="block text-gray-700 font-semibold mb-2">Phone Number</label>
                                <input type="tel" id="custom-phone" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose">
                            </div>
                            <button type="submit" class="w-full px-4 py-3 bg-rose text-white font-semibold rounded-lg shadow hover:bg-rose-dark transition-colors duration-200">Submit Request</button>
                        </form>
                    </div>
                `;
                document.getElementById('custom-order-form').addEventListener('submit', handleCustomOrderSubmit);
                document.getElementById('analyze-image-btn').addEventListener('click', handleAnalyzeImage);
                document.getElementById('custom-images').addEventListener('change', handleImagePreview);
            }
        }
        
        function renderAdminForm(collectionName, products) {
            mainContent.innerHTML = `
                <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Admin: Manage ${collectionName.replace('_products', '')}</h2>
                <div class="max-w-3xl mx-auto bg-white p-8 rounded-xl shadow-lg mb-8">
                    <form id="admin-product-form" class="space-y-6">
                        <input type="hidden" id="product-id">
                        <h3 class="text-xl font-semibold mb-4 text-gray-800">Add New Product</h3>
                        <div>
                            <label for="product-name" class="block text-gray-700 font-semibold mb-2">Product Name</label>
                            <input type="text" id="product-name" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose">
                        </div>
                        <div>
                            <label for="product-image" class="block text-gray-700 font-semibold mb-2">Product Image</label>
                            <input type="file" id="product-image" accept="image/*" class="w-full text-gray-700">
                        </div>
                        <div>
                            <label for="product-price" class="block text-gray-700 font-semibold mb-2">Price</label>
                            <input type="number" id="product-price" step="0.01" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-rose">
                        </div>
                        <div class="flex items-center">
                            <input type="checkbox" id="product-available" class="h-4 w-4 text-rose border-gray-300 rounded focus:ring-rose-500">
                            <label for="product-available" class="ml-2 block text-gray-700">Available</label>
                        </div>
                        <button type="submit" class="w-full px-4 py-3 bg-rose text-white font-semibold rounded-lg shadow hover:bg-rose-dark transition-colors duration-200">Save Product</button>
                    </form>
                </div>
                <h3 class="text-2xl font-bold text-center text-gray-800 mb-6">Existing Products</h3>
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
                    ${products.map(p => `
                        <div class="bg-white rounded-xl shadow-lg p-6 flex flex-col items-center text-center">
                            <img src="${p.imageUrl}" alt="${p.name}" class="w-full h-48 object-cover rounded-lg mb-4 border border-gray-200">
                            <h4 class="font-bold text-lg mb-2 text-gray-800">${p.name}</h4>
                            <p class="text-rose font-semibold mb-2">$${p.price.toFixed(2)}</p>
                            <p class="text-sm text-gray-500 mb-4">${p.available ? 'In Stock' : 'Out of Stock'}</p>
                            <div class="flex space-x-2">
                                <button data-id="${p.id}" class="edit-btn px-4 py-2 text-sm bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition-colors">Edit</button>
                                <button data-id="${p.id}" class="delete-btn px-4 py-2 text-sm bg-red-100 text-red-700 rounded-lg hover:bg-red-200 transition-colors">Delete</button>
                            </div>
                        </div>
                    `).join('')}
                </div>
            `;
            document.getElementById('admin-product-form').addEventListener('submit', (e) => handleProductFormSubmit(e, collectionName));
            document.querySelectorAll('.edit-btn').forEach(btn => btn.addEventListener('click', handleEditProduct));
            document.querySelectorAll('.delete-btn').forEach(btn => btn.addEventListener('click', (e) => handleDeleteProduct(e, collectionName)));
        }

        function renderAdminCustomOrders() {
            mainContent.innerHTML = `
                <h2 class="text-3xl font-bold text-center text-gray-800 mb-8">Admin: Custom Order Requests</h2>
                <div id="custom-orders-list" class="space-y-6">
                    ${customOrders.length > 0 ? customOrders.map(order => `
                        <div class="bg-white p-6 rounded-xl shadow-lg border border-gray-200">
                            <h3 class="font-bold text-xl mb-2 text-gray-800">Order from ${order.name}</h3>
                            <p class="text-sm text-gray-600 mb-4">Phone: ${order.phone}</p>
                            <p class="text-gray-700 mb-2 font-semibold">Description:</p>
                            <p class="text-gray-600 mb-4">${order.description}</p>
                            <p class="text-gray-700 mb-2 font-semibold">Estimated Price:</p>
                            <p class="text-gray-600 mb-4">${order.estimatedPrice || 'Not specified'}</p>
                            ${order.images && order.images.length > 0 ? `
                                <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 mt-4">
                                    ${order.images.map(imgSrc => `<img src="${imgSrc}" class="w-full h-auto rounded-lg border border-gray-300 object-cover" alt="Reference Image">`).join('')}
                                </div>
                            ` : ''}
                        </div>
                    `).join('') : '<p class="text-center text-gray-500 mt-10">No custom order requests yet.</p>'}
                </div>
            `;
        }

        // Function to handle image preview
        function handleImagePreview(event) {
            const previewContainer = document.getElementById('image-preview-container');
            previewContainer.innerHTML = ''; // Clear previous previews

            if (event.target.files) {
                Array.from(event.target.files).forEach(file => {
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        const img = document.createElement('img');
                        img.src = e.target.result;
                        img.classList.add('w-24', 'h-24', 'object-cover', 'rounded-lg', 'border', 'border-gray-300');
                        previewContainer.appendChild(img);
                    };
                    reader.readAsDataURL(file);
                });
            }
        }
        
        // --- Gemini API Function ---
        async function handleAnalyzeImage() {
            const imageInput = document.getElementById('custom-images');
            const descriptionTextarea = document.getElementById('custom-description');
            const analyzeBtn = document.getElementById('analyze-image-btn');

            if (imageInput.files.length === 0) {
                showModal('Error', 'Please upload at least one image to analyze.');
                return;
            }

            // Show loading state
            analyzeBtn.textContent = 'Analyzing...';
            analyzeBtn.disabled = true;

            const imageFile = imageInput.files[0];
            const reader = new FileReader();
            
            reader.onload = async () => {
                const base64ImageData = reader.result.split(',')[1];
                const prompt = "Analyze this image of a crochet pattern or item. Describe the object, suggest yarn colors, and estimate the complexity (e.g., Simple, Moderate, Complex) for a crocheter. Keep the response concise and friendly, focusing on key details.";
                
                const payload = {
                    contents: [
                        {
                            role: "user",
                            parts: [
                                { text: prompt },
                                {
                                    inlineData: {
                                        mimeType: imageFile.type,
                                        data: base64ImageData
                                    }
                                }
                            ]
                        }
                    ],
                };

                const apiKey = "";
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    
                    if (!response.ok) {
                        throw new Error(`API call failed with status: ${response.status}`);
                    }

                    const result = await response.json();
                    const text = result?.candidates?.[0]?.content?.parts?.[0]?.text || "Could not analyze the image. Please try again or describe it manually.";
                    
                    descriptionTextarea.value = text;
                    showModal('Analysis Complete', 'The image has been analyzed and the description field has been updated.');

                } catch (error) {
                    console.error('Error calling Gemini API:', error);
                    showModal('API Error', 'Something went wrong while analyzing the image. Please try again.');
                } finally {
                    // Reset loading state
                    analyzeBtn.textContent = 'Analyze Image ✨';
                    analyzeBtn.disabled = false;
                }
            };
            reader.readAsDataURL(imageFile);
        }

        // --- Form Submission Handlers ---
        async function handleCustomOrderSubmit(e) {
            e.preventDefault();
            const form = e.target;
            const description = form.elements['custom-description'].value;
            const estimatedPrice = form.elements['custom-price'].value;
            const name = form.elements['custom-name'].value;
            const phone = form.elements['custom-phone'].value;
            const imageFiles = form.elements['custom-images'].files;
            
            if (!name || !phone) {
                showModal('Error', 'Please enter your name and phone number.');
                return;
            }

            const images = await Promise.all(Array.from(imageFiles).map(file => {
                return new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.onload = () => resolve(reader.result);
                    reader.onerror = reject;
                    reader.readAsDataURL(file);
                });
            }));

            try {
                const customOrdersColRef = collection(db, `artifacts/${appId}/public/data/custom_orders`);
                await addDoc(customOrdersColRef, {
                    name,
                    phone,
                    description,
                    estimatedPrice,
                    images,
                    submittedAt: new Date(),
                });
                showModal('Success!', 'Your custom order request has been submitted. We will contact you shortly.');
                form.reset();
                document.getElementById('image-preview-container').innerHTML = ''; // Clear previews after submission
            } catch (error) {
                console.error("Error adding custom order: ", error);
                showModal('Error', 'Failed to submit your request. Please try again.');
            }
        }

        async function handleProductFormSubmit(e, collectionName) {
            e.preventDefault();
            const form = e.target;
            const id = form.elements['product-id'].value;
            const name = form.elements['product-name'].value;
            const price = parseFloat(form.elements['product-price'].value);
            const available = form.elements['product-available'].checked;
            const imageFile = form.elements['product-image'].files[0];
            
            let imageUrl = '';
            if (imageFile) {
                if (imageFile.size > 1024 * 1024 * 5) {
                    showModal('Error', 'Image file is too large. Max size is 5MB.');
                    return;
                }
                imageUrl = await new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.onload = () => resolve(reader.result);
                    reader.onerror = reject;
                    reader.readAsDataURL(imageFile);
                });
            }

            const productData = { name, price, available };
            if (imageUrl) {
                productData.imageUrl = imageUrl;
            }

            try {
                const productsColRef = collection(db, `artifacts/${appId}/users/${userId}/${collectionName}`);
                if (id) {
                    const docRef = doc(productsColRef, id);
                    await updateDoc(docRef, productData);
                    showModal('Success', 'Product updated successfully!');
                } else {
                    await addDoc(productsColRef, productData);
                    showModal('Success', 'New product added successfully!');
                }
                form.reset();
                form.elements['product-id'].value = '';
            } catch (error) {
                console.error("Error saving product: ", error);
                showModal('Error', 'Failed to save product. Please try again.');
            }
        }
        
        function handleEditProduct(e) {
            const id = e.target.dataset.id;
            const collectionName = currentView === 'purchase' ? 'purchase_products' : 'order_products';
            const products = currentView === 'purchase' ? purchaseProducts : orderProducts;
            const product = products.find(p => p.id === id);
            
            if (product) {
                document.getElementById('product-id').value = product.id;
                document.getElementById('product-name').value = product.name;
                document.getElementById('product-price').value = product.price;
                document.getElementById('product-available').checked = product.available;
                showModal('Editing', `Now editing "${product.name}". Update the fields and click Save.`);
            }
        }

        async function handleDeleteProduct(e, collectionName) {
            const id = e.target.dataset.id;
            try {
                const docRef = doc(db, `artifacts/${appId}/users/${userId}/${collectionName}`, id);
                await deleteDoc(docRef);
                showModal('Success', 'Product deleted successfully!');
            } catch (error) {
                console.error("Error deleting product: ", error);
                showModal('Error', 'Failed to delete product. Please try again.');
            }
        }

        // --- Event Listeners ---
        navButtons.purchase.addEventListener('click', () => { currentView = 'purchase'; renderView(); });
        navButtons.order.addEventListener('click', () => { currentView = 'order'; renderView(); });
        navButtons.custom.addEventListener('click', () => { currentView = 'custom'; renderView(); });

        adminLoginBtn.addEventListener('click', () => {
            passwordModal.classList.remove('hidden');
            passwordModal.classList.add('flex');
            passwordInput.value = '';
            passwordError.classList.add('hidden');
        });

        adminToggleBtn.addEventListener('click', () => {
            isAdmin = false;
            adminLoginBtn.classList.remove('hidden');
            adminToggleBtn.classList.add('hidden');
            notificationBell.classList.add('hidden');
            renderView();
        });

        passwordSubmitBtn.addEventListener('click', () => {
            const password = passwordInput.value;
            const correctPassword = "Xachiku1et";
            if (password === correctPassword) {
                isAdmin = true;
                adminLoginBtn.classList.add('hidden');
                adminToggleBtn.classList.remove('hidden');
                passwordModal.classList.remove('flex');
                passwordModal.classList.add('hidden');
                renderView();
            } else {
                passwordError.classList.remove('hidden');
            }
        });
        
        passwordCancelBtn.addEventListener('click', () => {
            passwordModal.classList.remove('flex');
            passwordModal.classList.add('hidden');
        });


        // Initial render on page load
        window.onload = () => {
            renderView();
            lucide.createIcons();
        };

        // Notification bell click handler
        notificationBell.addEventListener('click', () => {
            if (isAdmin) {
                currentView = 'custom';
                renderView();
            }
        });

    </script>
</body>
</html>
