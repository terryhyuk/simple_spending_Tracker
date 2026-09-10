# 🛠️ Piggy Log – QA & Technical Troubleshooting Log

This log documents technical issues I encountered while developing and maintaining **Piggy Log**.

Each case includes the issue, root cause, solution, and the main lesson learned from troubleshooting the problem. The cases cover state management, data handling, localization, UI behavior, performance, platform-specific issues, and code refactoring.

---

## 🔍 Section 1: Major Logic & Data Issues

### 🐛 #01. State Management & Real-Time UI Synchronization

**Context:** Global settings were not reflected immediately across the UI.

**Issue:**
When settings such as the monthly budget or currency format were changed, the dashboard and charts did not update immediately. The changes were only reflected after restarting the application.

**Root Cause:**
Some values were calculated only during the initial widget loading process. Changes made in the settings were not being propagated to the UI.

**Solution:**
Implemented reactive state management using **GetX Rx variables** and added update triggers so that dependent UI components could respond when global settings changed.

**Key Takeaway:**
Changing data and updating the UI are separate processes. State changes need to be explicitly connected to UI updates.

---

### 📅 #02. Date-Based Logic Conflicts

**Context:** Inconsistent results when selecting dates in the calendar.

**Issue:**
Selecting certain dates could display incorrect financial records, especially when navigating between dates or months.

**Root Cause:**
The database stored dates as strings in `yyyy-MM-dd` format, while the application compared them using complete `DateTime` objects that also contained time information.

**Solution:**
Standardized date-related comparisons using the same `yyyy-MM-dd` format before using the values in the UI.

**Key Takeaway:**
Date-related data should use a consistent format when moving between the database and application logic.

---

### 🗄️ #03. Data Integrity Issue During Record Updates

**Context:** A record became disconnected from its category after being edited.

**Issue:**
After editing a transaction from the calendar page, the record disappeared from the corresponding category list.

**Root Cause:**
The SQL query used by the calendar data handler did not include the `c_id` (Category ID) column. As a result, the category information was lost during the update process.

**Solution:**
Updated the query to include the required category information and adjusted the data mapping so that the category ID was preserved.

**Key Takeaway:**
When the same data can be accessed and modified from multiple screens, all required relational fields must be preserved throughout the data flow.

---

### 💱 #04. Inconsistent Currency Formatting

**Context:** Currency symbols and number formatting differed between screens.

**Issue:**
Currency symbols and number formats were not always consistent across different parts of the application.

**Root Cause:**
Formatting logic was implemented in multiple widgets instead of being handled in one place. This made it possible for different screens to use different formatting behavior.

**Solution:**
Centralized currency formatting in a utility class and connected the formatting behavior to the application's settings.

**Key Takeaway:**
Shared presentation rules should be centralized to avoid inconsistent behavior across screens.

---

## 📝 Section 2: UI, Localization & Component Issues

### 🌐 #05. Localization Layout Issues

**Context:** Supporting multiple languages with different text lengths.

**Issue:**
When switching the application to English or Japanese, some text became clipped or caused layout problems.

**Root Cause:**
Some layouts used fixed dimensions that were designed around Korean text lengths. Longer translated strings did not fit within those fixed areas.

**Solution:**
Removed unnecessary fixed dimensions and used Flutter layout widgets such as `Flexible` and `Expanded` to allow text and components to adapt to different lengths.

**Key Takeaway:**
Localization is not only a translation problem. UI layouts also need to accommodate different text lengths.

---

### 📊 #06. Chart Readability & Dark Mode

**Context:** Data visualization using `fl_chart`.

**Issue:**
Some chart labels were clipped, and certain chart elements were difficult to distinguish in Dark Mode.

**Root Cause:**
The default chart configuration did not provide enough padding for some labels, and the same visual settings were not suitable for both light and dark themes.

**Solution:**
Adjusted chart spacing and padding and applied different color settings for Light and Dark modes to improve readability.

**Key Takeaway:**
Data visualization should prioritize readability and contrast rather than relying only on default library settings.

---

### 🏗️ #07. Issues During Component Refactoring

**Context:** Separating UI components during refactoring.

**Issue:**
Some features stopped working correctly after UI components were separated from larger widgets.

**Root Cause:**
Some business and data-handling logic was located inside widgets. When the widgets were separated, references and data flow between components were also affected.

**Solution:**
Moved logic out of UI components and reorganized the code so that widgets focused primarily on UI rendering while state and application logic were handled separately.

**Key Takeaway:**
Separating UI and application logic makes future refactoring and maintenance easier.

---

### 🔋 #08. Animation Resource Management

**Context:** Continuous animations during tab navigation.

**Issue:**
A shake animation continued running after navigating away from the screen where it was being displayed.

**Root Cause:**
The animation controller remained active even when the related widget was no longer visible.

**Solution:**
Monitored the active tab state and controlled the animation based on whether the corresponding screen was visible. Animation resources were also stopped or disposed of when they were no longer needed.

**Key Takeaway:**
UI elements that are no longer visible should not continue performing unnecessary work in the background.

---

### 🧹 #09. Build Failures & Resource Naming

**Context:** Android build and resource issues after adding icon assets.

**Issue:**
The application repeatedly failed to build correctly after new icon resources were added, and some icons did not appear as expected.

**Root Cause:**
Some resource filenames did not follow Android's resource naming conventions, and stale build artifacts were also affecting the build.

**Solution:**
Renamed resource files using lowercase and underscore-based naming conventions and ran `flutter clean` before rebuilding the application.

**Key Takeaway:**
Platform-specific resource naming rules and build caches can cause issues that are unrelated to application logic.

---

### 📱 #10. Android App Icon Resource Configuration

**Context:** Android launcher icon configuration.

**Issue:**
An application launcher icon did not display correctly when an arbitrary resource name such as `icon.png` was used.

**Root Cause:**
The Android project configuration referenced the launcher icon using the expected `ic_launcher` resource name. The resource name and the manifest configuration did not match.

**Solution:**
Updated the icon resource configuration to use the expected launcher resource name and synchronized the reference in `AndroidManifest.xml`.

```xml
<application
    android:label="Piggy Log"
    android:icon="@mipmap/ic_launcher"
    android:roundIcon="@mipmap/ic_launcher">
</application>
```

**Key Takeaway:**
Platform-specific resource conventions and configuration references need to remain consistent.

---

## 💰 Section 3: Currency & Localization Logic

### 🪙 #11. Dynamic Currency Decimal Formatting

**Context:** Different currencies require different decimal precision.

**Issue:**
KRW and JPY were displayed with unnecessary decimal places, such as `₩1,000.00` instead of `₩1,000`.

**Root Cause:**
`NumberFormat.currency` was using two decimal places by default instead of considering the selected currency.

**Solution:**
Added currency-specific logic to determine the number of decimal places.

```dart
// Before
currencyFormat = NumberFormat.currency(
  locale: localeStr,
  symbol: symbol,
);

// After
int decimalDigits = (code == 'KRW' || code == 'JPY') ? 0 : 2;

currencyFormat = NumberFormat.currency(
  locale: localeStr,
  symbol: symbol,
  decimalDigits: decimalDigits,
);
```

**Key Takeaway:**
Currency formatting should consider the conventions of the selected currency rather than applying one fixed format to every currency.

---

### 🛡️ #12. Runtime Error Caused by Missing Currency Data

**Context:** Adding support for a new currency.

**Issue:**
Selecting Thai Baht (THB) caused a runtime error.

**Root Cause:**
THB was available as a UI option, but the corresponding currency data was missing from the currency map. An unsafe null assertion (`!`) then caused the application to fail when the value was not found.

**Solution:**
Added the missing THB data and used a fallback value with the null-coalescing operator (`??`) to prevent the application from failing when a currency key is unavailable.

```dart
// Before
final currencies = {
  'USD': {...},
  'KRW': {...},
};

final data = currencies[currencyCode]!;

// After
final currencies = {
  'USD': {...},
  'THB': {
    'symbol': '฿',
    'code': 'THB',
  },
  'KRW': {...},
};

final data = currencies[currencyCode] ?? currencies['USD']!;
```

**Key Takeaway:**
UI options and their underlying data must remain synchronized. Unsafe null assertions can turn missing configuration data into runtime failures.

---

### 🔀 #13. Refactoring Nested Ternary Logic

**Context:** Supporting multiple application languages.

**Issue:**
As the number of supported languages increased, nested ternary expressions became difficult to read and maintain.

**Root Cause:**
Multiple language conditions were implemented as a single nested ternary expression.

**Solution:**
Replaced the nested ternary expression with a Dart `switch` expression.

```dart
// Before
final String localeStr =
    lang == 'ko'
        ? 'ko_KR'
        : lang == 'ja'
            ? 'ja_JP'
            : lang == 'th'
                ? 'th_TH'
                : 'en_US';

// After
final String localeStr = switch (lang) {
  'ko' => 'ko_KR',
  'ja' => 'ja_JP',
  'th' => 'th_TH',
  _ => 'en_US',
};
```

**Key Takeaway:**
When conditional logic grows beyond a few simple cases, choosing a clearer control structure can improve readability and reduce maintenance problems.

---

## 📌 Summary

These troubleshooting cases cover several areas of practical application development:

* State management and UI synchronization
* Date and data consistency
* SQLite data integrity
* Shared formatting logic
* Localization and responsive layouts
* Data visualization
* Component refactoring
* Animation lifecycle management
* Android resource configuration
* Currency formatting
* Runtime null-safety issues
* Code readability and refactoring

Rather than treating these issues only as test failures, I used them to understand how data, application state, UI components, and platform-specific requirements interact within a real application.
