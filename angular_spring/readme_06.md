# Buy_From_Me

Welcome to **Buy_From_Me**, an e-commerce platform where users can create dynamic links for specific tabs (e.g., `buyfromme/zfs556zfse88ee`). These links are stored in a database and are accessible only to authenticated users who either own the tab or have been granted access. The frontend is built with **Angular**, while the backend leverages the power of **Spring Boot**.

## The Success Story
Everything seems perfect. The system is secure, the APIs are performing well, and users are loving the experience. The development team celebrates their success as the user base grows rapidly.

But then...

## Strange Feedback and a Growing Nightmare
### Stage 1: Unauthorized Access to Sensitive Data
Users start reporting strange behavior:

- Some users claim they can access tabs they shouldn’t have access to.
- Others report seeing sensitive data briefly before being redirected.

The team investigates the logic between the frontend and backend but finds no apparent issue. Local tests work flawlessly. The problem persists intermittently, and the team dismisses it as user error.

### Stage 2: Data Breach?!
Reports escalate. Some users claim their sensitive data was exposed without their action. This is alarming—it's not a simple bug; it's a **potential security vulnerability**.

The team realizes this isn't a coding error but a **malicious exploitation** of their authentication system.

## The Vulnerability: Exploiting a Race Condition
The app uses **Angular** for the frontend and **Spring Boot** for the backend. However, the authentication logic is executed outside Angular's `ngOnInit` lifecycle hook, leading to the following issue:

### The Problem:
- The page renders immediately, displaying **sensitive content**.
- The authentication check takes ~2 seconds to complete.
- If unauthorized, the content is hidden **after** the delay.

### The Exploit:
This brief window creates a **race condition** where an attacker could:

1. Access the dynamic link.
2. Inject malicious JavaScript into their browser to extract or manipulate content **before** the check completes.
3. Exploit the delay or disrupt the connection to **bypass authentication**.

## The Lesson: Secure Implementation with Angular Resolvers
To prevent this vulnerability, follow these best practices:

### Use Angular Resolvers
Resolvers ensure that authentication checks complete **before** rendering the page. Unauthorized users are blocked upfront, preventing any data exposure.

#### Create a Resolver
The resolver verifies the user's authorization to access a tab. Unauthorized users are redirected.

```typescript
import { Injectable } from '@angular/core';
import { Resolve, ActivatedRouteSnapshot, Router } from '@angular/router';
import { AuthService } from './auth.service';
import { Observable, of } from 'rxjs';
import { catchError, map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root',
})
export class TabAuthResolver implements Resolve<boolean> {
  constructor(private authService: AuthService, private router: Router) {}

  resolve(route: ActivatedRouteSnapshot): Observable<boolean> {
    const tabId = route.paramMap.get('id');
    return this.authService.isAuthorizedToViewTab(tabId).pipe(
      map((isAuthorized) => {
        if (isAuthorized) return true;
        this.router.navigate(['/unauthorized']);
        return false;
      }),
      catchError(() => {
        this.router.navigate(['/error']);
        return of(false);
      })
    );
  }
}
```

### Update the Routing Module
Attach the resolver to the route handling dynamic links.

```typescript
import { RouterModule, Routes } from '@angular/router';
import { TabComponent } from './tab/tab.component';
import { TabAuthResolver } from './tab-auth.resolver';

const routes: Routes = [
  {
    path: 'buyfromme/:id',
    component: TabComponent,
    resolve: { auth: TabAuthResolver },
  },
];

export const AppRoutingModule = RouterModule.forRoot(routes);
```

### Update the Component
The component focuses on displaying content only if the resolver grants access.

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-tab',
  templateUrl: './tab.component.html',
})
export class TabComponent implements OnInit {
  tabId: string;

  ngOnInit(): void {
    this.tabId = this.route.snapshot.paramMap.get('id');
  }
}
```

### Backend Authorization Check
The backend validates the user’s access to the tab.

```java
@Service
public class AuthService {
    @Autowired
    private TabRepository tabRepository;

    public boolean isAuthorizedToViewTab(String tabId, String userEmail) {
        Tab tab = tabRepository.findById(tabId).orElse(null);
        return tab != null && tab.getOwnerEmail().equals(userEmail);
    }
}
```

## A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties and will be considered a criminal.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.

Instead, use this knowledge to **secure** your applications and **educate others**.

## Conclusion
The **race condition vulnerability** in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks.

By following **secure practices** and **educating developers**, we can build resilient, trustworthy applications. Keep learning, stay curious, and stay secure!

### Happy Coding, and Secure Your Apps! 🚀

