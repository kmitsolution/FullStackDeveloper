Great. 👍 Let's implement **Angular pagination** now. Your ASP.NET Core API already supports pagination, so Angular only needs to send `pageNumber` and `pageSize`.

# Angular 21 — Step 5: Pagination

We'll display **3 products per page** so you can clearly see pagination working.

Flow:

```text
Angular
   │
   │ pageNumber=1&pageSize=3
   ▼
GET /api/products
   │
   ▼
ASP.NET Core
   │
   ▼
SQL Server
```

---

## 1. Update `ProductService`

Open:

```text
src/app/services/product.ts
```

Change `getProducts()` to accept page parameters:

```typescript
getProducts(
  pageNumber: number = 1,
  pageSize: number = 3
): Observable<any> {

  return this.http.get<any>(
    `${this.apiUrl}?pageNumber=${pageNumber}&pageSize=${pageSize}`
  );
}
```

So your complete service should be:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Product } from '../models/product';

@Injectable({
  providedIn: 'root'
})
export class ProductService {

  private apiUrl = 'http://localhost:5292/api/products';

  constructor(private http: HttpClient) {
  }

  getProducts(
    pageNumber: number = 1,
    pageSize: number = 3
  ): Observable<any> {

    return this.http.get<any>(
      `${this.apiUrl}?pageNumber=${pageNumber}&pageSize=${pageSize}`
    );
  }
}
```

---

# 2. Update `products.ts`

Open:

```text
src/app/pages/products/products.ts
```

Change your component to:

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
  pageSize = 3;
  totalPages = 0;

  constructor(private productService: ProductService) {
  }

  ngOnInit(): void {
    this.loadProducts();
  }

  loadProducts(): void {

    this.productService
      .getProducts(this.pageNumber, this.pageSize)
      .subscribe({
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

  nextPage(): void {

    if (this.pageNumber < this.totalPages) {
      this.pageNumber++;
      this.loadProducts();
    }
  }

  previousPage(): void {

    if (this.pageNumber > 1) {
      this.pageNumber--;
      this.loadProducts();
    }
  }
}
```

The important part is:

```typescript
this.pageNumber++;
this.loadProducts();
```

Angular changes the page number and calls the ASP.NET Core API again.

---

# 3. Update `products.html`

At the bottom of your product page, replace your current pagination information with:

```html
<div class="pagination">

  <button
    (click)="previousPage()"
    [disabled]="pageNumber === 1">
    Previous
  </button>

  <span>
    Page {{ pageNumber }} of {{ totalPages }}
  </span>

  <button
    (click)="nextPage()"
    [disabled]="pageNumber === totalPages">
    Next
  </button>

</div>
```

Your complete HTML can remain:

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

<div class="pagination">

  <button
    (click)="previousPage()"
    [disabled]="pageNumber === 1">
    Previous
  </button>

  <span>
    Page {{ pageNumber }} of {{ totalPages }}
  </span>

  <button
    (click)="nextPage()"
    [disabled]="pageNumber === totalPages">
    Next
  </button>

</div>
```

---

# 4. Add pagination CSS

At the bottom of `products.css`:

```css
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  margin: 30px 0;
}

.pagination button {
  padding: 10px 20px;
}

.pagination button:disabled {
  cursor: not-allowed;
  opacity: 0.5;
}
```

---

# 5. Test

Refresh:

```text
http://localhost:4200/products
```

You should initially see:

```text
Total Products: 7

iPhone 17
Galaxy S26
Dell Inspiron

Previous     Page 1 of 3     Next
```

Click **Next**:

```text
HP Pavilion
Lenovo ThinkPad
Samsung 55 inch TV

Previous     Page 2 of 3     Next
```

Click **Next** again:

```text
Apple AirPods

Previous     Page 3 of 3     Next
```

Notice what's happening:

```text
Page 1
   ↓
GET /api/products?pageNumber=1&pageSize=3

Page 2
   ↓
GET /api/products?pageNumber=2&pageSize=3

Page 3
   ↓
GET /api/products?pageNumber=3&pageSize=3
```

So you've now demonstrated a complete **server-side pagination flow** between Angular 21 and ASP.NET Core. 🎯

**Stop here and test Previous/Next.** Once that works, the next step will be **search + category + brand filtering from Angular**, using the filtering API we already built.
