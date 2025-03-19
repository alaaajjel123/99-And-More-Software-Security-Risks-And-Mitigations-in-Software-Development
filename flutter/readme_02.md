# Buy_From_Me

## Overview
Welcome to **Buy_From_Me**, an e-commerce platform where users can browse products, add items to their cart, and make purchases seamlessly. The frontend is built with **Flutter** for mobile, while the backend leverages **Firebase** for authentication and real-time features.

Our app uses **Firebase Authentication** for user management, issuing **JWT tokens** to authenticate and authorize users. Upon successful login, Firebase sends an authentication token to the app, which is then used to authorize user actions.

Everything seemed perfect. The app was working smoothly, user data was secure, and customers enjoyed a seamless shopping experience. The development team celebrated their hard work as the app went live.

But then...

---

## Strange Feedback and a Growing Nightmare

### Stage 1: Unauthorized Account Access
Users started reporting strange issues:

- Their accounts were being accessed from unknown devices or locations.
- Personal details were being updated without their knowledge.

The team investigated the app's behavior but found no immediate issues. Local tests worked flawlessly, and the authentication system seemed secure. The problem persisted intermittently, leading the team to initially dismiss it as user error or credential sharing.

### Stage 2: Data Leaks and Unfamiliar Transactions
Reports escalated. Some users claimed unauthorized transactions were being made from their accounts. This was alarming—this wasn't just a bug; it was a potential security vulnerability. The team realized this wasn’t a coding error but a malicious exploitation of their session management system.

---

## The Vulnerability: Improper Session Management in Firebase Authentication

### The Mistake:
- The app stored **Firebase JWT tokens** in **unprotected local storage** (e.g., `SharedPreferences` in Flutter).
- The team **didn’t implement a mechanism** to properly handle **token expiry** and refresh them when necessary.

### The Exploit:
- Attackers **decompiled the app** and extracted JWT tokens from local storage.
- Using these tokens, attackers **impersonated users** and performed unauthorized actions, such as updating personal details and making purchases.
- This led to **compromised user accounts** and **fraudulent transactions**.

---

## The Lesson: Secure Session Management
To prevent this vulnerability, follow these best practices:

### 🔒 **Avoid Storing Tokens in Local Storage**
Never store tokens in unprotected local storage (`SharedPreferences`, `localStorage`). Use **secure storage mechanisms** like `flutter_secure_storage` or platform-specific solutions (**iOS Keychain**, **Android Keystore**).

#### Example:
```dart
final storage = FlutterSecureStorage();
await storage.write(key: "auth_token", value: token);
```

### 🔄 **Implement Token Expiry and Refresh**
Ensure tokens **expire after a reasonable period** and refresh them using Firebase SDK:

```dart
FirebaseAuth.instance.currentUser?.getIdToken(true);
```

### ⏳ **Limit Session Duration**
- Set a **session timeout** for user sessions, especially for **sensitive actions**.
- Prompt users to **re-authenticate** after a certain period of inactivity.

### 🔑 **Use Secure HTTP Headers**
Always **send tokens via HTTPS** in HTTP headers to ensure secure transmission.

#### Example:
```dart
var response = await http.get(
  Uri.parse('https://api.buyfromme.com/user'),
  headers: {
    'Authorization': 'Bearer $token',
  },
);
```

### 🛡 **Obfuscate the App’s Source Code**
Use Flutter’s built-in **obfuscation** feature to make it harder for attackers to reverse-engineer the app.

#### Example:
```bash
flutter build apk --obfuscate --split-debug-info=<dir>
```

### 🚪 **Clear Tokens on Logout**
Ensure tokens are **properly cleared** when users log out.

#### Example:
```dart
await storage.delete(key: "auth_token");
```

---

## ⚠️ A Word of Caution
For hackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you could face severe penalties.
- **Every action leaves traces**; you may be tracked and held accountable.
- Instead of hacking, **use this knowledge to secure your applications** and educate others.

---

## 🎯 Conclusion
The improper session management vulnerability in the **Buy_From_Me** app serves as a critical reminder: **security lies in the details**. Always assume an attacker is looking for weak spots and design your systems to withstand even the most creative attacks. By following secure practices and educating developers, we can build **resilient, trustworthy applications**.

### Keep learning, stay curious, and stay secure! 🔐🚀

**Happy Coding, and Secure Your Apps!**