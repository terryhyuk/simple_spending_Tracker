# 🐷 Piggy Log (v1.3.0)

![Piggy Log Banner](./docs/metadata/piggy_log_image.png)

> A released personal finance application built with Flutter and SQLite.

**Piggy Log** is a personal expense tracking application available on both the **App Store** and **Google Play**.

It was developed as a local-first mobile application, focusing on practical expense management, data organization, localization, and maintainable application structure.

---

## ✨ Key Features

* Expense and income tracking
* Monthly budget management
* Category-based expense management
* Calendar-based expense history
* Dashboard and financial summaries
* Charts and data visualization
* Dark and light themes
* Data backup and restore
* English, Korean, Japanese, and Thai localization
* Local data storage without a login or backend server

---

## 📲 Download

<p align="left">
  <a href="https://apps.apple.com/app/piggy-log/id6757284836">
    <img src="https://developer.apple.com/assets/elements/badges/download-on-the-app-store.svg" height="50" alt="Download on App Store">
  </a>
  &nbsp;&nbsp;
  <a href="https://play.google.com/store/apps/details?id=com.terry.piggyLog">
    <img src="https://play.google.com/intl/en_us/badges/static/images/badges/en_badge_web_generic.png" height="50" alt="Get it on Google Play">
  </a>
</p>
---

## 🏗️ Architecture

Piggy Log is organized by shared application infrastructure, data models, feature-specific UI, and application state management.

```text
lib/
├── core/
│   ├── catalog/category/    # Category colors and icons
│   ├── database/            # SQLite database service and repositories
│   ├── utils/               # Shared application utilities
│   └── widget/              # Shared widgets
│
├── data/
│   └── models/              # Budget, category, record, and settings models
│
├── features/
│   ├── calendar/            # Calendar and date-based expense views
│   ├── categort/             # Category management
│   ├── dashboard/           # Dashboard and financial summaries
│   ├── onbording/            # Onboarding flow
│   ├── record/              # Expense and income records
│   └── settings/            # Application settings
│
├── l10n/                    # English, Japanese, Korean, and Thai localization
├── providers/               # Application state management with Provider
├── app.dart                 # Application configuration
└── main.dart                # Application entry point
```

The project separates reusable application code from feature-specific screens and state management, while SQLite-related services and repositories are grouped under `core/database`.

---

## 🔄 State Management Evolution

Piggy Log initially used **GetX** for state management and was later refactored to **Provider**.

The migration was part of improving the application's state management structure as the project became more complex.

The refactoring also provided an opportunity to review how application state is shared between different features and UI components.

---

## 💾 Database

Piggy Log initially used **Hive** for local data storage.

As the application's data structure became more complex, the database was migrated to **SQLite** to better manage relationships between the application's data.

The current database structure includes entities such as:

* Records
* Categories
* Budgets
* Settings

SQLite-related database services and repositories are organized under:

```text
lib/core/database/
├── database_service.dart
└── repository/
    ├── calendar_repository.dart
    ├── category_repository.dart
    ├── dashboard_repasitory.dart
    ├── record_repository.dart
    └── settings_repository.dart
```

---

## 🌍 Localization

Piggy Log supports four languages:

* 🇺🇸 English
* 🇰🇷 Korean
* 🇯🇵 Japanese
* 🇹🇭 Thai

Localization resources are managed under:

```text
lib/l10n/
├── app_en.arb
├── app_ja.arb
├── app_ko.arb
├── app_th.arb
├── app_localizations.dart
├── app_localizations_en.dart
├── app_localizations_ja.dart
├── app_localizations_ko.dart
└── app_localizations_th.dart
```

The application was tested with different language layouts and currency formats to support localized usage.

---

## 🎨 Technical Design Documents

### 1. [Logical Architecture](./docs/design/logical_architecture.png)

Shows the overall organization of the application's major components and state flow.

### 2. [Conceptual ERD](./docs/design/conceptual_ERD.png)

Shows the main data entities and their relationships used to structure the application's financial data.

### 3. [Physical DB Schema](./docs/design/physical_db_schema.png)

Shows the implemented SQLite database structure.

---

## 🧪 Testing & Troubleshooting

During development and maintenance, I documented issues found while testing the application and troubleshooting unexpected behavior.

The documented cases include areas such as:

* State synchronization
* Date and calendar logic
* Data integrity
* Currency formatting
* Localization
* UI layout
* Runtime errors
* Resource and build issues
* Refactoring-related issues

📑 **[View QA & Technical Troubleshooting Log](./QA_TROUBLESHOOTING.md)**

---

## 🛠️ Tech Stack

### Mobile

* Flutter
* Dart

### State Management

* Provider
* GetX

### Database

* SQLite
* Hive

### Development Tools

* Android Studio
* Xcode
* Git / GitHub

---

## 📌 Project Highlights

* Published a Flutter application on both iOS and Android
* Designed and implemented a local database structure
* Migrated local storage from Hive to SQLite
* Refactored state management from GetX to Provider
* Supported four languages
* Implemented backup and restore functionality
* Maintained the application through multiple development and testing cycles
* Documented technical issues and troubleshooting processes

---

## 📬 Contact

**Terry Yoon**

📧 [yonghyuk.terry.yoon@gmail.com](mailto:yonghyuk.terry.yoon@gmail.com)
📍 Vancouver, BC, Canada
