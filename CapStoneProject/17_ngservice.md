Yes. 👍 Since you're using **Angular 21**, we should use the current Angular structure and terminology.

You're correct: with the newer Angular CLI, generating a service gives you:

```text
src/app/services/product.ts
```

rather than the older:

```text
product.service.ts
```

Let's rewrite **Angular Step 2** specifically for Angular 21.

## Angular 21 — Step 2: Product Service

Our goal remains:

```text
Angular 21
    │
    │ HttpClient
    ▼
ASP.NET Core API
/api/products
    │
    ▼
SQL Server
```

### 1. Generate the service

From your Angular project:

```powershell
ng generate service services/product
```

You should get:

```text
src/
└── app/
    └── services/
        ├── product.ts
        └── product.spec.ts
```

We'll use **`product.ts`**.

---

### 2. Configure HttpClient

Angular 21 uses the standalone application configuration.

Open:

```text
src/app/app.config.ts
```

Make sure you have:

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

The important part is:

```typescript
provideHttpClient()
```

This allows our services to use `HttpClient`.

---

### 3. Create the Product model

Create this folder:

```text
src/app/models
```

Then create:

```text
src/app/models/product.ts
```

Add:

```typescript
export interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  pictureUrl: string;
  category: string;
  brand: string;
}
```

This corresponds to our ASP.NET Core `ProductDto`.

---

### 4. Update `services/product.ts`

Open:

```text
src/app/services/product.ts
```

Replace its contents with:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Product } from '../models/product';

@Injectable({
  providedIn: 'root'
})
export class Product {

  private apiUrl = 'https://localhost:YOUR_PORT/api/products';

  constructor(private http: HttpClient) {
  }

  getProducts(): Observable<any> {
    return this.http.get<any>(this.apiUrl);
  }
}
```

### One small Angular naming point

Because Angular generated:

```text
product.ts
```

the class is also currently:

```typescript
export class Product
```

But **we should change that** because `Product` is already our model/interface.

Use:

```typescript
export class ProductService
```

So the complete file should actually be:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Product } from '../models/product';

@Injectable({
  providedIn: 'root'
})
export class ProductService {

  private apiUrl = 'https://localhost:YOUR_PORT/api/products';

  constructor(private http: HttpClient) {
  }

  getProducts(): Observable<any> {
    return this.http.get<any>(this.apiUrl);
  }
}
```

The **filename can remain `product.ts`**. That's perfectly fine.

---

### 5. Replace `YOUR_PORT`

For example, if your ASP.NET Core application runs at:

```text
https://localhost:7123
```

then:

```typescript
private apiUrl = 'https://localhost:7123/api/products';
```

---

### What we have now

```text
src/app
│
├── models
│   └── product.ts
│
└── services
    └── product.ts
```

And conceptually:

```text
models/product.ts
       │
       │ Product interface
       ▼
services/product.ts
       │
       │ HttpClient
       ▼
ASP.NET Core /api/products
```

**Stop here for now.** Don't create the product component yet.

Make sure the project compiles with:

```powershell
ng serve
```

If there are no errors, tell me **done**, and we'll do the next small step: **Angular 21 Product List component to display the products page-wise**.
