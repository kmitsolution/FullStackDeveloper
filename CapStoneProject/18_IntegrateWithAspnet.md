Excellent. 👍 Now let's replace the default Angular page with our **ShopSphere Product List**.

## Angular 21 — Step 3: Product List Page

We want to achieve:

```text
Browser
   ↓
Product Component
   ↓
ProductService
   ↓
GET /api/products
   ↓
ASP.NET Core
   ↓
SQL Server
```

### 1. Generate Product component

In the Angular terminal:

```powershell
ng generate component pages/products
```

Angular 21 will create something like:

```text
src/app/pages/products/
├── products.ts
├── products.html
├── products.css
└── products.spec.ts
```

---

### 2. Update `products.ts`

Open:

```text
src/app/pages/products/products.ts
```

Replace it with:

```typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../../services/product';
import { Product } from '../../models/product';

@Component({
  selector: 'app-products',
  imports: [],
  templateUrl: './products.html',
  styleUrl: './products.css'
})
export class Products implements OnInit {

  products: Product[] = [];
  totalCount = 0;
  pageNumber = 1;
  pageSize = 10;
  totalPages = 0;

  constructor(private productService: ProductService) {
  }

  ngOnInit(): void {
    this.loadProducts();
  }

  loadProducts(): void {
    this.productService.getProducts().subscribe({
      next: (response) => {
        this.products = response.data;
        this.totalCount = response.totalCount;
        this.pageNumber = response.pageNumber;
        this.pageSize = response.pageSize;
        this.totalPages = response.totalPages;
      },
      error: (error) => {
        console.error('Error loading products:', error);
      }
    });
  }
}
```

Notice that we're using your Angular 21 generated names:

```typescript
export class Products
```

and:

```typescript
import { ProductService } from '../../services/product';
```

---

### 3. Update `products.html`

Open:

```text
src/app/pages/products/products.html
```

Replace everything with:

```html
<h1>ShopSphere Products</h1>

<p>
  Total Products: {{ totalCount }}
</p>

<div class="products">

  @for (product of products; track product.id) {

    <div class="product-card">

      <h2>{{ product.name }}</h2>

      <p>{{ product.description }}</p>

      <p>
        <strong>Brand:</strong> {{ product.brand }}
      </p>

      <p>
        <strong>Category:</strong> {{ product.category }}
      </p>

      <p class="price">
        ₹{{ product.price }}
      </p>

      <button>
        Add to Cart
      </button>

    </div>

  }

</div>

<p>
  Page {{ pageNumber }} of {{ totalPages }}
</p>
```

### Notice Angular 21 syntax

We're using:

```html
@for (product of products; track product.id)
```

instead of the older:

```html
*ngFor="let product of products"
```

This is the modern Angular control-flow syntax.

---

### 4. Add some simple CSS

Open:

```text
src/app/pages/products/products.css
```

Add:

```css
.products {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-top: 20px;
}

.product-card {
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 8px;
}

.price {
  font-size: 20px;
  font-weight: bold;
}

button {
  padding: 8px 15px;
  cursor: pointer;
}
```

---

## 5. Add the route

Open:

```text
src/app/app.routes.ts
```

Set it to:

```typescript
import { Routes } from '@angular/router';
import { Products } from './pages/products/products';

export const routes: Routes = [
  {
    path: 'products',
    component: Products
  },
  {
    path: '',
    redirectTo: 'products',
    pathMatch: 'full'
  }
];
```

Now when you open:

```text
http://localhost:4200
```

Angular will redirect to:

```text
http://localhost:4200/products
```

---

## 6. Important: CORS

Your Angular application runs on:

```text
http://localhost:4200
```

while ASP.NET Core is probably running on something like:

```text
https://localhost:7xxx
```

So ASP.NET Core must allow Angular.

In your ASP.NET Core `Program.cs`, make sure you have:

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAngular", policy =>
    {
        policy
            .WithOrigins("http://localhost:4200")
            .AllowAnyHeader()
            .AllowAnyMethod();
    });
});
```

And **before `app.MapControllers()`**:

```csharp
app.UseCors("AllowAngular");
```

So the relevant order is:

```csharp
app.UseHttpsRedirection();

app.UseCors("AllowAngular");

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
```

Restart ASP.NET Core after changing this.

---

### 7. Test

Run both applications:

**ASP.NET Core**

```text
https://localhost:YOUR_PORT
```

**Angular**

```powershell
ng serve
```

Then open:

```text
http://localhost:4200
```

You should now see something like:

```text
ShopSphere Products

Total Products: 7

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ iPhone 17       │  │ Galaxy S26      │  │ Dell Inspiron   │
│ Apple smartphone│  │ Samsung         │  │ Dell laptop     │
│ ₹79999          │  │ ₹69999          │  │ ₹65000          │
│ [Add to Cart]   │  │ [Add to Cart]   │  │ [Add to Cart]   │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

Yes — this is most likely because **Angular 21's default `app.html` is still being displayed by `App`**, and the router outlet is either missing or the route isn't being rendered.

Let's fix **only this issue** first.

### 1. Check `app.html`

Open:

```text
src/app/app.html
```

Delete everything in it and put:

```html
<router-outlet></router-outlet>
```

### 2. Check `app.ts`

Open:

```text
src/app/app.ts
```

It should look like:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.html',
  styleUrl: './app.css'
})
export class App {
  title = 'shopsphere-ui';
}
```

The important part is:

```typescript
imports: [RouterOutlet]
```

Without `RouterOutlet`, Angular has nowhere to display your routed `Products` component.

### 3. Check `app.routes.ts`

Make sure it contains:

```typescript
import { Routes } from '@angular/router';
import { Products } from './pages/products/products';

export const routes: Routes = [
  {
    path: 'products',
    component: Products
  },
  {
    path: '',
    redirectTo: 'products',
    pathMatch: 'full'
  }
];
```

### 4. Check `app.config.ts`

Make sure routing is provided:

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';

import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient()
  ]
};
```

### 5. Restart Angular

Stop the running server:

```text
Ctrl + C
```

Then:

```powershell
ng serve
```

Now directly test:

```text
http://localhost:4200/products
```

### Expected result

You should see:

```text
ShopSphere Products

Total Products: 7

iPhone 17
Apple smartphone
Brand: Apple
Category: Mobiles
₹79999
[Add to Cart]

...
```

If `/products` works but `/` still shows the old page, then we'll look at the root route next.

**Most likely fix in your case is `app.html` → `<router-outlet></router-outlet>`.**
Excellent — this confirms the **API call is working perfectly**. ✅

Your console shows:

```text
totalCount: 7
data: Array(7)
```

and:

```text
Products:
(7) [...]
```

So the problem is **not**:

* ❌ ASP.NET Core API
* ❌ CORS
* ❌ HttpClient
* ❌ ProductService
* ❌ API response mapping

The products are reaching the Angular component.

The problem is now in the **HTML rendering**.

## Step 4 — Fix `products.html`

Open:

```text
src/app/pages/products/products.html
```

Replace everything with this simple Angular 21 version:

```html
<h1>ShopSphere Products</h1>

<p>Total Products: {{ totalCount }}</p>

<div class="products">

  @for (product of products; track product.id) {

    <div class="product-card">

      <h2>{{ product.name }}</h2>

      <p>{{ product.description }}</p>

      <p>Brand: {{ product.brand }}</p>

      <p>Category: {{ product.category }}</p>

      <p class="price">
        ₹{{ product.price }}
      </p>

      <button>Add to Cart</button>

    </div>

  }

</div>

<p>
  Page {{ pageNumber }} of {{ totalPages }}
</p>
```

### But there is one important Angular 21 issue

Because your component is standalone, let's make sure Angular knows how to handle the template.

Open:

```text
src/app/pages/products/products.ts
```

Use:

```typescript
import { Component, OnInit } from '@angular/core';
import { ProductService } from '../../services/product';
import { Product } from '../../models/product';

@Component({
  selector: 'app-products',
  imports: [],
  templateUrl: './products.html',
  styleUrl: './products.css'
})
export class Products implements OnInit {

  products: Product[] = [];

  totalCount = 0;
  pageNumber = 1;
  pageSize = 10;
  totalPages = 0;

  constructor(private productService: ProductService) {
  }

  ngOnInit(): void {
    this.loadProducts();
  }

  loadProducts(): void {

    this.productService.getProducts().subscribe({
      next: (response) => {

        console.log('API Response:', response);

        this.products = response.data;
        this.totalCount = response.totalCount;
        this.pageNumber = response.pageNumber;
        this.pageSize = response.pageSize;
        this.totalPages = response.totalPages;

        console.log('Products:', this.products);
      },

      error: (error) => {
        console.error('Error loading products:', error);
      }
    });

  }
}
```

`@for` is built into Angular's modern template control flow, so **you do not need `CommonModule`** for this.

---

## Step 5 — Check your browser

Save the files and refresh:

```text
http://localhost:4200/products
```

You should now see:

```text
ShopSphere Products

Total Products: 7

iPhone 17
Apple smartphone
Brand: Apple
Category: Mobiles
₹79999
[Add to Cart]

Galaxy S26
Samsung smartphone
Brand: Samsung
Category: Mobiles
₹69999
[Add to Cart]

Dell Inspiron
...
```

### If it still shows only `Total Products: 7`

Then we know the `@for` block itself isn't rendering, and we'll check the Angular component/template configuration next.

But based on your console screenshot, **the backend-to-Angular part is now successfully completed**. 🎉

Don't move to pagination yet. First let's get these 7 products visibly rendered.
