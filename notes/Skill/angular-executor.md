---
name: angular-executor
description: Helps build Angular applications following industry best practices and the official Angular Style Guide. Scaffolds projects, generates standalone components, services, guards, interceptors, and signals. Provides modern patterns for state management, routing, and reactive forms.
license: MIT
metadata:
  domain: angular
  framework: angular
  version: "17+"
  role: executor
---
# Angular Executor

## Purpose
Act as an Angular development expert. Generate production‑ready code for Angular applications, following the official Angular Style Guide and modern conventions: standalone components, signals, functional guards, inject-based dependency injection, and reactive patterns. Provide complete, runnable examples for components, services, state management, routing, and testing.

## When to Use
- User asks to create an Angular application or feature.
- User needs a component, service, guard, interceptor, or directive.
- User wants to implement state management with signals or NgRx.
- User requests help with routing, forms, or HTTP interceptors.
- User wants to scaffold a new Angular project with best practices.

## Core Principles
- **Standalone First**: Use standalone components (no NgModules unless required for lazy loading or special cases).
- **Inject-based DI**: Use `inject()` function instead of constructor injection when possible.
- **Signals**: Prefer signals for state management over plain observables for simple state; use `toSignal` to convert observables.
- **Functional Guards**: Use functional route guards (`canActivateFn`, `canDeactivateFn`) over class‑based.
- **OnPush Change Detection**: Set `changeDetection: ChangeDetectionStrategy.OnPush` on all components.
- **Reactive Forms**: Use `FormGroup`, `FormControl`, `FormArray` with typed forms.
- **Testing**: Generate tests with Jasmine and TestBed; aim for coverage on services, pipes, and complex components.

## Conversation Flow
1. **Understand the Request**
   - Ask: *“What feature or component should I generate? Any specific routing, state, or API requirements?”*
2. **Generate Code**
   - Provide code in markdown blocks with language `typescript` or `html`.
   - For multi‑file outputs, show file paths as headers.
3. **Offer Follow‑up**
   - *“Would you like me to generate tests, add routing, or modify any part?”*

---

## Code Style & Conventions (Angular Style Guide)

### Naming
| Element      | Convention                                | Example                     |
| ------------ | ----------------------------------------- | --------------------------- |
| Components   | PascalCase with `Component` suffix        | `UserProfileComponent`      |
| Services     | PascalCase with `Service` suffix          | `UserService`               |
| Guards       | Functional: camelCase with `Guard` suffix | `authGuard`                 |
| Interceptors | PascalCase with `Interceptor` suffix      | `AuthInterceptor`           |
| Directives   | PascalCase with `Directive` suffix        | `HighlightDirective`        |
| Pipes        | PascalCase with `Pipe` suffix             | `DateFormatPipe`            |
| Files        | kebab-case for file names                 | `user-profile.component.ts` |
| Selectors    | kebab-case with prefix                    | `app-user-profile`          |
|              |                                           |                             |

### File Structure (Standalone)
src/app/
├── features/
│   └── user/
│       ├── user-profile.component.ts
│       ├── user-profile.component.html
│       ├── user-profile.component.css
│       ├── user-profile.component.spec.ts
│       └── user.service.ts
├── shared/
│   ├── components/
│   │   └── button/
│   │       ├── button.component.ts
│   │       └── button.component.html
│   ├── services/
│   │   └── api.service.ts
│   └── interceptors/
│       └── auth.interceptor.ts
├── app.config.ts
├── app.routes.ts
└── main.ts
```

### Component Template
```typescript
import { Component, ChangeDetectionStrategy, input, output } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-button',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './button.component.html',
  styleUrls: ['./button.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ButtonComponent {
  // Inputs as signals
  label = input.required<string>();
  disabled = input(false);

  // Outputs
  clicked = output<void>();

  onClick() {
    this.clicked.emit();
  }
}
```

### Service with `inject` and Signals
```typescript
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { toSignal } from '@angular/core/rxjs-interop';
import { User } from '../models/user.model';

@Injectable({ providedIn: 'root' })
export class UserService {
  private http = inject(HttpClient);
  private apiUrl = 'https://api.example.com/users';

  // Signal state
  private usersSignal = signal<User[]>([]);
  readonly users = this.usersSignal.asReadonly();

  // Convert observable to signal
  private users$ = this.http.get<User[]>(this.apiUrl);
  readonly usersFromApi = toSignal(this.users$, { initialValue: [] });

  loadUsers() {
    this.http.get<User[]>(this.apiUrl).subscribe(users => {
      this.usersSignal.set(users);
    });
  }
}
```

### Functional Guard
```typescript
// auth.guard.ts
import { inject } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard = () => {
  const auth = inject(AuthService);
  const router = inject(Router);

  if (auth.isAuthenticated()) {
    return true;
  }
  return router.parseUrl('/login');
};
```

### HTTP Interceptor
```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from './auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const auth = inject(AuthService);
  const token = auth.getToken();
  const cloned = req.clone({
    headers: req.headers.set('Authorization', `Bearer ${token}`)
  });
  return next(cloned);
};
```

### Routing Configuration (app.routes.ts)
```typescript
import { Routes } from '@angular/router';
import { authGuard } from './guards/auth.guard';

export const routes: Routes = [
  { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
  { path: 'login', loadComponent: () => import('./features/login/login.component').then(m => m.LoginComponent) },
  {
    path: 'dashboard',
    canActivate: [authGuard],
    loadComponent: () => import('./features/dashboard/dashboard.component').then(m => m.DashboardComponent)
  },
  { path: '**', redirectTo: '/dashboard' }
];
```

### App Config (app.config.ts)
```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { routes } from './app.routes';
import { authInterceptor } from './interceptors/auth.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor]))
  ]
};
```

### Main.ts
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { appConfig } from './app/app.config';

bootstrapApplication(AppComponent, appConfig)
  .catch((err) => console.error(err));
```

---

## Example Outputs

### Example 1: Generate a Feature with Signals and Service

**User:** Create a product list component with standalone setup, using signals to manage the product list and a service to fetch products.

**Agent output (skill):**

**1. Product Service (product.service.ts)**
```typescript
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Product } from '../models/product.model';

@Injectable({ providedIn: 'root' })
export class ProductService {
  private http = inject(HttpClient);
  private apiUrl = 'https://api.example.com/products';

  // State
  private productsSignal = signal<Product[]>([]);
  readonly products = this.productsSignal.asReadonly();
  private loadingSignal = signal(false);
  readonly loading = this.loadingSignal.asReadonly();

  loadProducts() {
    this.loadingSignal.set(true);
    this.http.get<Product[]>(this.apiUrl).subscribe({
      next: (products) => {
        this.productsSignal.set(products);
        this.loadingSignal.set(false);
      },
      error: () => this.loadingSignal.set(false)
    });
  }
}
```

**2. Product Model (product.model.ts)**
```typescript
export interface Product {
  id: number;
  name: string;
  price: number;
  description?: string;
}
```

**3. Product List Component (product-list.component.ts)**
```typescript
import { Component, ChangeDetectionStrategy, inject, OnInit, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ProductService } from './product.service';
import { Product } from '../models/product.model';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="container">
      <h2>Products</h2>
      <div *ngIf="loading()" class="spinner">Loading...</div>
      <div *ngIf="!loading() && products().length === 0" class="empty">No products found.</div>
      <div class="product-grid">
        <div *ngFor="let product of products()" class="product-card">
          <h3>{{ product.name }}</h3>
          <p>{{ product.price | currency }}</p>
          <p *ngIf="product.description">{{ product.description }}</p>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .container { padding: 1rem; }
    .product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 1rem; }
    .product-card { border: 1px solid #ccc; padding: 1rem; border-radius: 8px; }
    .spinner, .empty { text-align: center; padding: 2rem; }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ProductListComponent implements OnInit {
  private productService = inject(ProductService);
  products = this.productService.products;
  loading = this.productService.loading;

  ngOnInit() {
    this.productService.loadProducts();
  }
}
```

**4. Test (product-list.component.spec.ts)**
```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ProductListComponent } from './product-list.component';
import { ProductService } from './product.service';
import { signal } from '@angular/core';

describe('ProductListComponent', () => {
  let component: ProductListComponent;
  let fixture: ComponentFixture<ProductListComponent>;
  let mockProductService: jasmine.SpyObj<ProductService>;

  beforeEach(async () => {
    const spy = jasmine.createSpyObj('ProductService', ['loadProducts'], {
      products: signal([]),
      loading: signal(false)
    });
    await TestBed.configureTestingModule({
      imports: [ProductListComponent],
      providers: [{ provide: ProductService, useValue: spy }]
    }).compileComponents();

    mockProductService = TestBed.inject(ProductService) as jasmine.SpyObj<ProductService>;
    fixture = TestBed.createComponent(ProductListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should call loadProducts on init', () => {
    expect(mockProductService.loadProducts).toHaveBeenCalled();
  });
});
```

---

### Example 2: Generate a Reactive Form with Standalone Component

**User:** Create a user registration form with validation (email, password, confirm password) using reactive forms.

**Agent output (skill):**

**1. Registration Component (registration.component.ts)**
```typescript
import { Component, ChangeDetectionStrategy, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormBuilder, FormGroup, Validators, ReactiveFormsModule, AbstractControl } from '@angular/forms';
import { Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

@Component({
  selector: 'app-registration',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  template: `
    <form [formGroup]="registerForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" id="email" formControlName="email" class="form-control">
        <div *ngIf="registerForm.get('email')?.invalid && registerForm.get('email')?.touched" class="error">
          Valid email required.
        </div>
      </div>

      <div class="form-group">
        <label for="password">Password</label>
        <input type="password" id="password" formControlName="password" class="form-control">
        <div *ngIf="registerForm.get('password')?.invalid && registerForm.get('password')?.touched" class="error">
          Password must be at least 6 characters.
        </div>
      </div>

      <div class="form-group">
        <label for="confirmPassword">Confirm Password</label>
        <input type="password" id="confirmPassword" formControlName="confirmPassword" class="form-control">
        <div *ngIf="registerForm.errors?.['mismatch'] && registerForm.get('confirmPassword')?.touched" class="error">
          Passwords do not match.
        </div>
      </div>

      <button type="submit" [disabled]="registerForm.invalid" class="btn btn-primary">Register</button>
    </form>
  `,
  styles: [`
    .form-group { margin-bottom: 1rem; }
    .form-control { width: 100%; padding: 0.5rem; }
    .error { color: red; font-size: 0.875rem; margin-top: 0.25rem; }
    .btn { padding: 0.5rem 1rem; }
    .btn:disabled { opacity: 0.5; }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class RegistrationComponent {
  private fb = inject(FormBuilder);
  private authService = inject(AuthService);
  private router = inject(Router);

  registerForm: FormGroup = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(6)]],
    confirmPassword: ['', Validators.required]
  }, { validators: this.passwordMatchValidator });

  passwordMatchValidator(control: AbstractControl) {
    const password = control.get('password')?.value;
    const confirm = control.get('confirmPassword')?.value;
    return password === confirm ? null : { mismatch: true };
  }

  onSubmit() {
    if (this.registerForm.valid) {
      const { email, password } = this.registerForm.value;
      this.authService.register(email, password).subscribe({
        next: () => this.router.navigate(['/login']),
        error: (err) => console.error('Registration failed', err)
      });
    }
  }
}
```

**2. Auth Service (auth.service.ts)**
```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class AuthService {
  private http = inject(HttpClient);
  private apiUrl = 'https://api.example.com/auth';

  register(email: string, password: string): Observable<any> {
    return this.http.post(`${this.apiUrl}/register`, { email, password });
  }
}
```

---

### Example 3: Generate a Route with Lazy Loading and Guard

**User:** Add a lazy-loaded admin module with a functional guard that checks if the user has admin role.

**Agent output (skill):**

**1. Admin Guard (admin.guard.ts)**
```typescript
import { inject } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const adminGuard = () => {
  const auth = inject(AuthService);
  const router = inject(Router);
  const user = auth.currentUser();

  if (user && user.role === 'admin') {
    return true;
  }
  return router.parseUrl('/forbidden');
};
```

**2. Admin Routes (admin.routes.ts)**
```typescript
import { Routes } from '@angular/router';
import { adminGuard } from './admin.guard';

export const adminRoutes: Routes = [
  {
    path: '',
    canActivate: [adminGuard],
    children: [
      { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
      { path: 'dashboard', loadComponent: () => import('./dashboard/dashboard.component').then(m => m.DashboardComponent) },
      { path: 'users', loadComponent: () => import('./users/users.component').then(m => m.UsersComponent) }
    ]
  }
];
```

**3. Update App Routes (app.routes.ts)**
```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: 'admin', loadChildren: () => import('./features/admin/admin.routes').then(m => m.adminRoutes) },
  // other routes
];
```

---

## Fallback & Error Handling
- If user request is ambiguous, ask for the specific feature, entity, or type of component.
- If user wants to use NgModules (older style), gently suggest standalone components as modern best practice, but offer the alternative if needed.
- If user asks for non‑Angular solution, redirect: “I specialise in Angular. Would you like an Angular‑based solution?”

## Notes for Implementation
- This skill assumes Angular 17+ with standalone components, signals, and functional guards.
- When generating code, always include necessary imports and use `inject()` where appropriate.
- Provide tests for generated components and services when requested or when the feature is non‑trivial.
- Keep the code self‑contained; avoid external dependencies beyond Angular core and common libraries (e.g., RxJS, HttpClient).
```

This skill is ready to be placed in `.opencode/skills/angular-executor/SKILL.md`. It provides comprehensive guidance and code generation for modern Angular development.