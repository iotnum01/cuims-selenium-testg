# CUIMS Automated Testing Framework (Selenium + TestNG + Maven)

An enterprise-grade, end-to-end automated testing framework for the **Chandigarh University Information Management System (CUIMS)** (`https://students.cuchd.in/`), built with **Selenium WebDriver 4**, **Java 21**, **TestNG**, and **ExtentReports 5**.

---

## 1. Project Architecture (Page Object Model)

The framework follows the **Page Object Model (POM)** design pattern with a clean separation of concerns:

```
cuims-selenium-testng/
│
├── pom.xml                               # Maven project definition, plugins & dependencies
├── testng.xml                           # TestNG suite runner with ExtentReports listener
│
├── src/main/java/com/cuims/
│   ├── base/
│   │   ├── BasePage.java                # Reusable WebDriver wrappers, explicit waits, JS fallback
│   │   └── BaseTest.java                # Lifecycle hooks (@BeforeMethod, @AfterMethod, ThreadLocal driver)
│   ├── pages/
│   │   ├── LoginPage.java               # CUIMS authentication form elements & actions
│   │   ├── DashboardPage.java           # Student dashboard & navigation sidebar
│   │   ├── AttendancePage.java          # Attendance calculation & warning badges
│   │   └── TimetablePage.java           # Timetable views & download buttons
│   ├── utils/
│   │   ├── ConfigReader.java            # Reads configurations from config.properties
│   │   ├── ScreenshotUtil.java          # Timestamped screenshot utility
│   │   └── CaptchaHandler.java          # Automation strategies for CAPTCHA
│   └── listeners/
│       └── ExtentReportListener.java    # Generates rich interactive HTML reports
│
├── src/main/resources/
│   ├── config.properties                # Browser, URL, timeout configurations
│   └── log4j2.xml                       # Structured logging configuration
│
├── src/test/java/com/cuims/tests/
│   ├── NavigationSmokeTest.java         # HTTPS security & responsive checks
│   ├── LoginTest.java                   # Authentication, UI validation, Data-Driven tests
│   └── AttendanceTest.java              # Attendance formula validation & business rule tests
│
├── src/test/resources/testdata/
│   └── login_data.csv                   # CSV test data for parameterized runs
│
├── reports/                             # Generated HTML Test Reports (ExtentReports)
└── screenshots/                         # Automated failure screenshots
```

---

## 2. Test Plan & Requirements Traceability Matrix (RTM)

| Test ID | Module | Test Description | Category | Expected Result |
| :--- | :--- | :--- | :--- | :--- |
| `TC_SMOKE_01` | Infrastructure | Verify CUIMS loads securely over HTTPS | Smoke | URL starts with `https://` |
| `TC_SMOKE_02` | UI / Layout | Verify portal page title & window viewport dimensions | Smoke / UI | Title is not empty, window maximized |
| `TC_AUTH_01` | Authentication | Verify all core login UI elements exist (UID, Logo, Link) | Smoke / UI | Elements visible on page |
| `TC_AUTH_02` | Authentication | Verify password input is masked and toggle behaves properly | Security | `type="password"` by default |
| `TC_AUTH_03` | Authentication | Verify empty credential submission triggers validation | Negative | Remains on login page; alert displayed |
| `TC_AUTH_04` | Authentication | Data-driven invalid login attempts (SQLi, invalid UID, short pwd) | Negative / DDT | Access denied, dashboard not reached |
| `TC_ATT_01` | Attendance | Verify attendance calculation formula `(Attended/Delivered)*100` | Functional | Calculated percentage equals expected value |
| `TC_ATT_02` | Attendance | Verify business rule: Attendance `< 75%` triggers warning | Business Rule | Debarred/Warning flag raised when `< 75%` |

---

## 3. Addressing the CUIMS CAPTCHA Challenge

In automated testing of production systems with CAPTCHA:
1. **Human-in-the-Loop Mode (Default in `config.properties`)**: The test fills your UID and Password, pauses, and allows you to enter the CAPTCHA in the open browser window. Automation automatically detects the dashboard URL transition and resumes.
2. **Session Cookie Injection**: After logging in manually once, the session cookies (`ASP.NET_SessionId`) can be injected directly into WebDriver to bypass the login screen completely for downstream testing (Attendance, Timetable, Marks).

---

## 4. How to Run the Tests

### Option A: In IntelliJ IDEA / Eclipse / VS Code (Recommended)
1. Open IntelliJ IDEA or Eclipse.
2. Click **File > Open** and select the folder:
   `C:\Users\rajpu\.gemini\antigravity\scratch\cuims-selenium-testng`
3. The IDE will recognize the `pom.xml` and automatically download all dependencies (Selenium, TestNG, ExtentReports).
4. Right-click on **`testng.xml`** and choose **Run 'testng.xml'**.

### Option B: From Command Line via Maven
Once Apache Maven is installed on your PATH:
```bash
# Navigate to project root
cd C:\Users\rajpu\.gemini\antigravity\scratch\cuims-selenium-testng

# Run all tests configured in testng.xml
mvn clean test

# Run a specific test group (e.g. Smoke tests only)
mvn test -Dgroups=smoke

# Run with Edge or Firefox
mvn test -Dbrowser=edge
```

---

## 5. Test Execution Reports

After running the tests:
- Interactive HTML report is saved in:
  `reports/CUIMS_Test_Report_<TIMESTAMP>.html`
- Any failed test steps automatically capture full-screen screenshots and embed them directly inside the ExtentReport HTML.
