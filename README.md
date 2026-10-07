const products = [
  {
    id: 1,
    name: "Smart Watch X12",
    category: "electronics",
    tag: "Best Seller",
    rating: 4.8,
    sold: 1240,
    price: 899000,
    oldPrice: 1199000,
    image:
      "https://images.unsplash.com/photo-1546868871-7041f2a55e12?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 2,
    name: "Kaos Premium Cotton",
    category: "fashion",
    tag: "New",
    rating: 4.7,
    sold: 980,
    price: 129000,
    oldPrice: 199000,
    image:
      "https://images.unsplash.com/photo-1521572267360-ee0c2909d518?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 3,
    name: "Lampu LED Smart Home",
    category: "home",
    tag: "Promo",
    rating: 4.9,
    sold: 760,
    price: 240000,
    oldPrice: 390000,
    image:
      "https://images.unsplash.com/photo-1505693416388-ac5ce068fe85?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 4,
    name: "Skin Care Set Glow",
    category: "beauty",
    tag: "Limited",
    rating: 4.8,
    sold: 1540,
    price: 320000,
    oldPrice: 520000,
    image:
      "https://images.unsplash.com/photo-1522335789203-aabd1fc54bc9?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 5,
    name: "Kipas Angin DC",
    category: "home",
    tag: "Hot",
    rating: 4.6,
    sold: 432,
    price: 560000,
    oldPrice: 780000,
    image:
      "https://images.unsplash.com/photo-1581578731548-c64695cc6952?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 6,
    name: "Air Fryer Mini",
    category: "groceries",
    tag: "Flash",
    rating: 4.8,
    sold: 690,
    price: 780000,
    oldPrice: 990000,
    image:
      "https://images.unsplash.com/photo-1585518419759-7fe2e0fbf8a6?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 7,
    name: "Headphone Noise Cancelling",
    category: "electronics",
    tag: "Popular",
    rating: 4.9,
    sold: 910,
    price: 640000,
    oldPrice: 899000,
    image:
      "https://images.unsplash.com/photo-1546435770-a3e426bf472b?auto=format&fit=crop&w=900&q=80",
  },
  {
    id: 8,
    name: "Set Kosmetik Minimalis",
    category: "beauty",
    tag: "Trending",
    rating: 4.7,
    sold: 1120,
    price: 260000,
    oldPrice: 410000,
    image:
      "https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&w=900&q=80",
  },
];

const productGrid = document.getElementById("productGrid");
const searchInput = document.getElementById("searchInput");
const cartButton = document.getElementById("cartButton");
const cartDrawer = document.getElementById("cartDrawer");
const closeCart = document.getElementById("closeCart");
const overlay = document.getElementById("overlay");
const cartItems = document.getElementById("cartItems");
const cartCount = document.getElementById("cartCount");
const subtotalEl = document.getElementById("subtotal");
const totalPriceEl = document.getElementById("totalPrice");

const state = {
  category: "all",
  search: "",
  cart: [],
};

function currency(value) {
  return new Intl.NumberFormat("id-ID", {
    style: "currency",
    currency: "IDR",
    maximumFractionDigits: 0,
  }).format(value);
}

function renderProducts() {
  const filtered = products.filter((product) => {
    const matchesCategory = state.category === "all" || product.category === state.category;
    const matchesSearch =
      product.name.toLowerCase().includes(state.search.toLowerCase()) ||
      product.category.toLowerCase().includes(state.search.toLowerCase());
    return matchesCategory && matchesSearch;
  });

  productGrid.innerHTML = filtered
    .map(
      (product) => `
        <article class="product-card">
          <div class="product-image" style="background-image: url('${product.image}')">
            <button class="wishlist-btn" type="button" aria-label="Simpan favorit">♡</button>
          </div>
          <div class="product-body">
            <div class="tag-row">
              <span class="product-tag">${product.tag}</span>
              <span class="sold-count">${product.sold}+ terjual</span>
            </div>
            <h3 class="product-name">${product.name}</h3>
            <div class="rating-row">
              <span class="star">★</span>
              <span>${product.rating}</span>
            </div>
            <div class="price-row">
              <span class="current-price">${currency(product.price)}</span>
              <span class="old-price">${currency(product.oldPrice)}</span>
            </div>
            <button type="button" data-add="${product.id}">Tambah ke Keranjang</button>
          </div>
        </article>
      `
    )
    .join("");

  if (!filtered.length) {
    productGrid.innerHTML = '<div class="empty-cart" style="grid-column: 1 / -1;">Produk tidak ditemukan.</div>';
  }
}

function updateCart() {
  cartCount.textContent = String(state.cart.reduce((sum, item) => sum + item.qty, 0));

  if (!state.cart.length) {
    cartItems.innerHTML = '<div class="empty-cart">Keranjang masih kosong.</div>';
    subtotalEl.textContent = currency(0);
    totalPriceEl.textContent = currency(0);
    return;
  }

  cartItems.innerHTML = state.cart
    .map(
      (item) => `
        <div class="cart-item">
          <div class="cart-item-image" style="background-image: url('${item.image}')"></div>
          <div>
            <h4>${item.name}</h4>
            <div class="price">${currency(item.price)}</div>
            <div class="qty-box">
              <button type="button" data-qty-minus="${item.id}">−</button>
              <span>${item.qty}</span>
              <button type="button" data-qty-plus="${item.id}">+</button>
            </div>
          </div>
          <div class="price">${currency(item.price * item.qty)}</div>
        </div>
      `
    )
    .join("");

  const subtotal = state.cart.reduce((sum, item) => sum + item.price * item.qty, 0);
  subtotalEl.textContent = currency(subtotal);
  totalPriceEl.textContent = currency(subtotal);
}

function addToCart(productId) {
  const product = products.find((item) => item.id === Number(productId));
  if (!product) return;

  const existing = state.cart.find((item) => item.id === product.id);

  if (existing) {
    existing.qty += 1;
  } else {
    state.cart.push({ ...product, qty: 1 });
  }

  openCart();
  updateCart();
}

function changeQty(productId, delta) {
  const item = state.cart.find((cartItem) => cartItem.id === Number(productId));
  if (!item) return;

  item.qty += delta;
  if (item.qty <= 0) {
    state.cart = state.cart.filter((cartItem) => cartItem.id !== Number(productId));
  }

  updateCart();
}

function openCart() {
  cartDrawer.classList.add("open");
  overlay.classList.add("show");
}

function closeDrawer() {
  cartDrawer.classList.remove("open");
  overlay.classList.remove("show");
}

searchInput.addEventListener("input", (event) => {
  state.search = event.target.value.trim();
  renderProducts();
});

cartButton.addEventListener("click", openCart);
closeCart.addEventListener("click", closeDrawer);
overlay.addEventListener("click", closeDrawer);

document.addEventListener("click", (event) => {
  const addButton = event.target.closest("[data-add]");
  if (addButton) {
    addToCart(addButton.dataset.add);
  }

  const qtyPlus = event.target.closest("[data-qty-plus]");
  if (qtyPlus) {
    changeQty(qtyPlus.dataset.qtyPlus, 1);
  }

  const qtyMinus = event.target.closest("[data-qty-minus]");
  if (qtyMinus) {
    changeQty(qtyMinus.dataset.qtyMinus, -1);
  }

  const categoryButton = event.target.closest(".category-card");
  if (categoryButton) {
    document.querySelectorAll(".category-card").forEach((btn) => btn.classList.remove("active"));
    categoryButton.classList.add("active");
    state.category = categoryButton.dataset.category || "all";
    renderProducts();
  }
});

renderProducts();
updateCart();

window.addEventListener("DOMContentLoaded", () => {
  renderProducts();
  updateCart();
});

