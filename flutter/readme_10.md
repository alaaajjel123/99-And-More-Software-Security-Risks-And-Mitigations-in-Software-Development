# Buy_From_Me

Welcome to **Buy_From_Me**, a mobile e-commerce application built with Flutter that allows users to browse products, add items to their cart, and place orders seamlessly. The development team was thrilled with its smooth performance, and the app was successfully deployed to production.

Everything seemed perfect. The system was secure, the APIs were performing well, and users were loving the experience. The development team celebrated their success as the user base grew rapidly.

---

## Strange User Complaints and a Growing Nightmare

### Stage 1: Strange Pop-Ups and Unauthorized Access

Users started reporting strange issues:

- Some users complained about strange pop-ups appearing when they opened the app.
- Others noticed unauthorized access to their accounts.
- The app began requesting unnecessary permissions, such as reading SMS messages.

The team investigated but initially found no apparent issues in the app’s code or backend systems. Local tests worked flawlessly, and the problem seemed sporadic. The team dismissed it as isolated incidents, possibly caused by user error.

### Stage 2: Escalation and Investigation

As complaints increased, the situation became more alarming. Some users reported that their accounts were compromised, and unauthorized changes were made to their profiles. The team realized this wasn’t a user error or an isolated bug—it was a security vulnerability.

Upon further investigation, the team discovered that a third-party Flutter package used for handling background notifications had an undisclosed security flaw. This package allowed remote code execution through an exploit in its dependencies.

---

## The Vulnerability: Malware Execution via a Vulnerable Package

The core issue stemmed from the fact that the app relied on a third-party Flutter package (**flutter_notifications_plus**) that had been compromised. This package introduced hidden malware that executed during app startup, leading to:

### Malicious Code Execution:
- A malicious actor could execute arbitrary code inside the app.
- The app could be forced to request additional dangerous permissions.

### Sensitive Data Exposure:
- Sensitive user data, such as login credentials, could be compromised.

### Unwanted Ads and Phishing:
- Malicious pop-ups tricked users into entering their credentials.

---

## The Exploit: How Attackers Exploited the Vulnerable Package

### 1. The Malicious Package Update:
- The **flutter_notifications_plus** package had been compromised when a hacker injected malicious code into one of its dependencies.
- The app was set up to automatically fetch the latest versions of dependencies, so the malicious code was introduced without the team’s knowledge.

### 2. Exploiting Auto-Updates:
- When the vulnerable package was updated, it introduced hidden malware that executed during app startup.
- The malicious code injected hidden ads or phishing pop-ups disguised as app content.

### 3. Code Execution on User Devices:
- The malware silently sent user credentials to an external server.
- It requested excessive permissions (e.g., location, SMS, storage) without user knowledge.

---

## The Lesson: Secure Dependency Management and Code Auditing

To prevent attacks resulting from vulnerable third-party packages, it is essential to implement strict dependency management and code auditing procedures within the **Buy_From_Me** app. The app should validate every package used to ensure that only trusted packages are integrated.

---

## Mitigation Steps: Preventing Malware Execution via Vulnerable Packages

### 1. Strict Dependency Management:
- Pin dependency versions instead of auto-updating. Specify exact versions in `pubspec.yaml`.
- Regularly audit dependencies for security vulnerabilities before updating.

#### Example of safe dependency management:
```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_notifications_plus: 3.2.1 # Use a pinned, reviewed version
```

### 2. Code Auditing for Third-Party Packages:
- Always check the package maintainers and recent commit history before adding a new dependency.
- Use Flutter’s `pub.dev` security score to evaluate package trustworthiness.
- Run security analysis tools like `flutter analyze` and static code scanning tools.

### 3. Restrict App Permissions:
- Follow the principle of least privilege—only request permissions when necessary.
- Enforce runtime permission checks instead of requesting all permissions at install time.

#### Example:
```dart
void requestStoragePermission() async {
  if (await Permission.storage.request().isGranted) {
    // Proceed with file access
  } else {
    // Handle permission denial
  }
}
```

### 4. Monitor Network Activity in the App:
- Log network requests to detect unexpected outbound traffic.
- Use **certificate pinning** to prevent the app from connecting to unauthorized servers.

### 5. Educate Developers on Supply Chain Security:
- Train the team on supply chain attacks and package hijacking risks.
- Establish internal security policies for reviewing third-party code before integration.

---

## A Word of Caution

For attackers or anyone considering exploiting such vulnerabilities:

- **Hacking is illegal and unethical.** If caught, you are subject to severe penalties.
- **Every action leaves traces;** you may be tracked and held accountable for your actions.
- Instead, use this knowledge to **secure your applications and educate others.**

---

## Conclusion

This incident was a wake-up call for the **Buy_From_Me** team. Relying on third-party dependencies without proper security checks can be dangerous. By implementing strict dependency management, code auditing, and permission controls, the team has strengthened the app’s defenses and rebuilt user trust.

### Happy Coding, and Secure Your Apps! 🚀

