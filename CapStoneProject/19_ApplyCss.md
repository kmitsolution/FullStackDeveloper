Great. 👍 Now that the **7 products are successfully coming from ASP.NET Core and displaying in Angular 21**, let's move to the next small step.

# Angular 21 — Step 4: Product Cards

We will now turn the simple product list into a proper product catalog.

Our page will look like:

```text
ShopSphere Products

┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│   Product Image  │  │   Product Image  │  │   Product Image  │
│                  │  │                  │  │                  │
│ iPhone 17        │  │ Galaxy S26       │  │ Dell Inspiron    │
│ Apple            │  │ Samsung          │  │ Dell             │
│ ₹79,999          │  │ ₹69,999          │  │ ₹65,000          │
│ [Add to Cart]    │  │ [Add to Cart]    │  │ [Add to Cart]    │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

We won't implement **Add to Cart** yet. The button will be visual only.

---

## 1. Update `products.html`

Replace your temporary HTML with:

```html
<h1>ShopSphere Products</h1>

<p>Total Products: {{ totalCount }}</p>

<div class="products">

  @for (product of products; track product.id) {

    <div class="product-card">

      <div class="product-image">
        <img
          [src]="product.pictureUrl"
          [alt]="product.name">
      </div>

      <div class="product-info">

        <h2>{{ product.name }}</h2>

        <p>{{ product.description }}</p>

        <p>
          <strong>Brand:</strong> {{ product.brand }}
        </p>

        <p>
          <strong>Category:</strong> {{ product.category }}
        </p>

        <h3>
          ₹{{ product.price }}
        </h3>

        <button>
          Add to Cart
        </button>

      </div>

    </div>

  }

</div>

<div class="pagination-info">
  Page {{ pageNumber }} of {{ totalPages }}
</div>
```

---

## 2. Add CSS

Open:

```text
src/app/pages/products/products.css
```

Replace it with:

```css
.products {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-top: 20px;
}

.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  padding: 15px;
}

.product-image {
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f5f5;
}

.product-image img {
  max-width: 100%;
  max-height: 180px;
}

.product-info {
  padding: 10px;
}

.product-info h2 {
  margin-bottom: 8px;
}

.product-info h3 {
  font-size: 22px;
}

button {
  padding: 10px 20px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

.pagination-info {
  margin-top: 25px;
  margin-bottom: 25px;
}
```

### One issue with the images

Your backend currently returns values such as:

```text
images/iphone17.jpg
```

But those images **don't actually exist in the Angular application's `assets`/public folder yet**.

Therefore the product information will appear, but the images may show as broken.

That's okay for this step.

We'll handle product images properly later.

---

# 3. Test

Refresh:

```text
http://localhost:4200/products
```

You should now have the product cards.

You should see **7 products**.

---

## What we've achieved

The flow is now:

```text
SQL Server
    ↓
EF Core
    ↓
Product Repository
    ↓
ASP.NET Core API
    ↓
Angular HttpClient
    ↓
ProductService
    ↓
Products Component
    ↓
Product Cards
```

That's a very important full-stack connection. 🎯

### Next step after this

We'll implement **pagination**.

Your backend already supports:

```text
pageNumber
pageSize
totalCount
totalPages
```

So Angular will call:

```text
/api/products?pageNumber=1&pageSize=3
```

then provide:

```text
[Previous]  Page 1 of 3  [Next]
```

This will also demonstrate how **Angular controls ASP.NET Core API query parameters**.

For now, get the product cards displaying correctly.
