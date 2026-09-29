# Deposit Mobile Banking App

Native mobile banking app for a **bank in Denmark**. Customers open and manage **deposit/savings accounts**, move money between their Basis account and their savings, and handle their profile, all secured with **MitID** login.

The app is built natively on **both platforms**:

| Platform | Language | UI | IDE | Architecture |
|---|---|---|---|---|
| **Android** (this repository) | Kotlin | XML layouts + ViewBinding / DataBinding, Material 3 | Android Studio | MVVM + Clean Architecture |
| **iOS** | Swift | SwiftUI | Xcode | MVVM |

Both apps use the same backend REST APIs, the same MitID login flow, the same design, and support **English and Danish**.

---

## Screenshots

| Splash | Login | New customer |
|:---:|:---:|:---:|
| <img src="screenshots/01_splash.png" width="220"/> | <img src="screenshots/02_login.png" width="220"/> | <img src="screenshots/03_new_customer.png" width="220"/> |

<!-- The screens below require a MitID login. Add them to /screenshots and uncomment the rows.
| Home | Savings | Profile |
|:---:|:---:|:---:|
| <img src="screenshots/04_home.png" width="220"/> | <img src="screenshots/05_savings.png" width="220"/> | <img src="screenshots/06_profile.png" width="220"/> |

| Security code | PIN login | Deposit account details |
|:---:|:---:|:---:|
| <img src="screenshots/07_security_code.png" width="220"/> | <img src="screenshots/08_pin_login.png" width="220"/> | <img src="screenshots/09_deposit_details.png" width="220"/> |
-->

---

## Features

### Login and security
- **MitID login** (via Criipto) in a secure Custom Tab, with an App Link redirect back to the app
- **4-digit security code (PIN)** for fast login on later visits, with a create / verify / reset flow
- **Biometric login** (Face ID / fingerprint) using BiometricPrompt
- Consent screen for fetching data from public registers
- Automatic **access-token refresh** and session expiry handling
- Tokens and user data stored in **EncryptedSharedPreferences** (AES-256)

### Home
- **Basis account card** showing the balance, registration and account number (tap to copy), with Transfer and Withdraw actions
- List of **deposit accounts** with the interest rate (fixed-term or variable) and balance, plus a collapsible "closed accounts" section
- **Special offers / campaigns** on existing savings accounts
- Create a **new deposit account** in a 3-step bottom sheet
- **Re-KYC** reminder when the customer's due-diligence review date is near

### Savings
- Overview of all savings accounts, account details and statements
- Withdraw from a deposit account, and transfer into or withdraw from the Basis account
- Transaction signing with MitID

### Customer onboarding (new customers)
- Full digital onboarding with MitID: personal details, address, citizenship, tax, occupation, monthly income, source of funds, purpose of savings and expected deposit amount
- Phone number verification with an **OTP** code (SMS Retriever)
- Onboarding can be interrupted and resumed later

### Profile
- Profile header with the customer's photo (camera or gallery upload), total balance and active accounts
- **Edit profile**: update email and phone number (the name and address come from MitID)
- **Security & login**: change the security code and turn biometric login on or off
- Documents, agreements, notifications, referrals and help
- Log out

### General
- **English and Danish**, following the device language (amounts and numbers use the local format)
- **Multiple environments**: Dev, QA, UAT, Migration, Prod
- No-internet detection and friendly error messages
- Lottie loading animations

---

## Tech stack – Android

| Area | Technology |
|---|---|
| Language | **Kotlin** (JVM 11) |
| IDE / build | **Android Studio**, Gradle Kotlin DSL (AGP 8.7), version catalog |
| SDK | minSdk 26, target / compile SDK 36 |
| Architecture | **MVVM + Clean Architecture** (data / domain / presentation layers, one use case per action) |
| Dependency injection | **Dagger-Hilt** |
| Asynchronous work | **Kotlin Coroutines**, **Flow / StateFlow** |
| Networking | **Retrofit**, **OkHttp** (auth interceptor, logging), **Gson** |
| Lists | RecyclerView, **Paging 3** |
| Jetpack | ViewModel, LiveData, Lifecycle, Navigation, ViewBinding, DataBinding, SplashScreen API |
| Security | **EncryptedSharedPreferences** (security-crypto), **BiometricPrompt**, network security config (no cleartext traffic) |
| Authentication | **MitID via Criipto** (OpenID Connect), Chrome Custom Tabs, Android App Links |
| UI | Material 3, XML layouts, Montserrat font, Flexbox, **Glide**, **Lottie** |
| Other | Google SMS Retriever (OTP autofill) |
| CI | **GitHub Actions**: secret scan (gitleaks), Android Lint, build of the debug APK |

## Tech stack – iOS

| Area | Technology |
|---|---|
| Language | **Swift** |
| IDE | **Xcode** |
| UI | **SwiftUI** (reusable components, property wrappers for state management) |
| Architecture | **MVVM** |
| Networking | **URLSession** with **async/await**, **Codable** |
| Security | Keychain, MitID login with `ASWebAuthenticationSession` |
| Environments | Xcode build configurations / schemes (QA, UAT, Prod) |
| Distribution | **TestFlight** via App Store Connect |

---

## Project structure (Android)

```
app/src/main/java/<package>/depositmobilebanking/
├── data/
│   ├── data_source/      # Retrofit API interface
│   ├── remote/           # AuthInterceptor (headers, token refresh)
│   ├── local/            # SessionManager (tokens, expiry)
│   ├── network/          # NetworkMonitor
│   └── repository/       # Repository implementation
├── domain/
│   ├── model/            # Request / response models
│   ├── repository/       # Repository interface
│   ├── use_cases/        # One use case per API action
│   └── paging_source/    # Paging 3 sources
├── presentation/         # Activities, Fragments, ViewModels, Adapters
│   ├── splash/  login/  onboarding/  home/  savings/
│   ├── profile/  messages/  help/  rekyc/ ...
├── di/                   # Hilt modules (Retrofit, Repository)
└── utils/                # Constants, SecurePrefs, extensions
```

**Data flow:** Fragment/Activity → ViewModel (`StateFlow` UI state) → UseCase (`Flow<ResponseState<T>>`) → Repository → Retrofit API.

---

## Getting started (Android)

1. Clone the repository and open it in **Android Studio** (latest stable version).
2. Copy `local.properties.example` to `local.properties` and fill in the values for each environment: `BASE_URL_*`, `AUTH_URL_*`, `SIGNUP_URL_*`, `SUB_KEY_*` and the optional signing keys. **Never commit `local.properties`.**
3. Pick a build variant, for example **qaDebug**, and run the app.

```bash
./gradlew assembleQaDebug      # build the QA debug APK
./gradlew lintDevDebug         # run lint (same check as CI)
```

### Build variants
| Flavor | Use |
|---|---|
| `dev` | Local backend |
| `qa` | QA / test environment |
| `uat` | User acceptance testing |
| `migration` | Data-migration testing |
| `prod` | Production |

---

## My role

I worked on this project as an **Application Developer**, building the app natively for **Android (Kotlin, Android Studio)** and **iOS (Swift, SwiftUI, Xcode)**:
- converted UI/UX designs into screens on both platforms, following MVVM;
- integrated the REST APIs and the MitID / OAuth redirect login;
- built the Home, deposit accounts, transfers, KYC and profile features;
- set up QA, UAT and Production environments, and distributed iOS beta builds with TestFlight;
- worked in an Agile team using Git, Jira and code reviews.
