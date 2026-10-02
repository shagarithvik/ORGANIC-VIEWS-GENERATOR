# ⚒️ WebSessionForge

### Multi-Session WebView2 Browser & Proxy Testing Platform

[![.NET](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet\&logoColor=white)](https://dotnet.microsoft.com/)
[![Windows](https://img.shields.io/badge/Windows-10%2F11-0078D4?logo=windows\&logoColor=white)](https://www.microsoft.com/windows)
[![WPF](https://img.shields.io/badge/UI-WPF-5C2D91)](https://learn.microsoft.com/dotnet/desktop/wpf/)
[![WebView2](https://img.shields.io/badge/WebView2-Microsoft-0078D7?logo=microsoftedge\&logoColor=white)](https://developer.microsoft.com/microsoft-edge/webview2/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> **WebSessionForge is a .NET 8 WPF platform for running isolated WebView2 browser sessions with proxy management, health monitoring, rotation, failure recovery, and session-level observability.**

---

## 🚀 Overview

**WebSessionForge** is a Windows desktop application built with:

* **C#**
* **.NET 8**
* **WPF**
* **Microsoft Edge WebView2**

It provides a centralized environment for creating and managing multiple isolated browser sessions while independently managing their proxy assignments.

The platform combines:

```text
Browser Sessions
       +
Proxy Management
       +
Health Monitoring
       +
Rotation
       +
Failure Recovery
       +
Observability
```

into a single desktop application.

The project is designed for **authorized QA, browser-session experimentation, reliability testing, proxy infrastructure testing, and controlled web automation research**.

---

# ✨ Core Features

## 🌐 Multi-Session WebView2

Run multiple independent WebView2 browser environments from a single application.

Each session can maintain its own:

* Browser environment
* Profile/state
* Proxy assignment
* Session identifier
* Runtime state
* Logging stream
* Health information

### Session Architecture

```text
                    WebSessionForge
                          │
             ┌────────────┼────────────┐
             │            │            │
             ▼            ▼            ▼
         Session 1    Session 2    Session N
             │            │            │
             ▼            ▼            ▼
          WebView2      WebView2      WebView2
             │            │            │
             ▼            ▼            ▼
          Proxy A      Proxy B      Proxy N
```

---

# 🔄 Proxy Management

WebSessionForge maintains a normalized proxy pool that can be used by browser sessions.

Supported proxy schemes:

* HTTP
* HTTPS
* SOCKS4
* SOCKS5

Proxy records can contain:

| Field    | Description               |
| -------- | ------------------------- |
| Host     | Proxy hostname/IP         |
| Port     | Proxy port                |
| Scheme   | HTTP/HTTPS/SOCKS4/SOCKS5  |
| Username | Optional authentication   |
| Password | Optional authentication   |
| Provider | Proxy source              |
| Latency  | Measured response latency |
| Health   | Current health state      |
| Failures | Failure count             |

---

# ❤️ Proxy Health Monitoring

Before proxies are assigned to sessions, WebSessionForge can validate their availability.

The health system performs protocol-appropriate checks and tracks:

* Healthy proxies
* Failed proxies
* Response latency
* Failure count
* Proxy availability
* Provider health

### Health Pipeline

```text
Proxy Source
     │
     ▼
Parse
     │
     ▼
Normalize
     │
     ▼
Deduplicate
     │
     ▼
Protocol Validation
     │
     ▼
Health Check
     │
 ┌───┴────┐
 ▼        ▼
Healthy   Failed
 │        │
 ▼        ▼
Pool     Remove
 │
 ▼
Session Assignment
```

---

# ♻️ Proxy Rotation

Sessions can rotate their proxy assignment according to configurable intervals.

Supported functionality includes:

* Rotation intervals
* Rotation jitter
* Proxy reservation
* Proxy release
* Replacement proxies
* Concurrent-session protection

This allows controlled testing of applications and browser sessions under changing network conditions.

---

# 📥 CSV Proxy Import

Proxy lists can be imported from CSV files.

### Endpoint format

```csv
proxy
socks5://203.0.113.10:1080
http://203.0.113.11:8080
```

Recognized endpoint fields include:

```text
proxy
endpoint
address
```

### Structured format

```csv
host,port,scheme,username,password
203.0.113.10,1080,socks5,,
203.0.113.11,8080,http,user,secret
```

Common aliases are also supported:

```text
ip
protocol
user
pass
```

Imported proxies are:

```text
Parsed
  ↓
Normalized
  ↓
Deduplicated
  ↓
Validated
  ↓
Health Checked
  ↓
Added to Proxy Pool
```

---

# 🔌 Proxy Provider Integration

Proxy provider endpoints can be configured through:

```text
config/config.json
```

The provider system can consume common formats including:

### JSON Array

```json
[
  "http://host:port",
  "socks5://host:port"
]
```

### JSON Object

```json
{
  "proxies": [
    "http://host:port",
    "socks5://host:port"
  ]
}
```

### Plain Text

```text
http://host1:port
socks5://host2:port
```

Comma-separated endpoints are also supported.

---

# 🧠 Architecture

```text
                         ┌─────────────────────┐
                         │   WebSessionForge   │
                         │     WPF Dashboard   │
                         └──────────┬──────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
              ▼                     ▼                     ▼
       Session Manager        Proxy Manager           Logger
              │                     │                     │
              │              ┌──────┴──────┐              │
              │              │             │              │
              ▼              ▼             ▼              ▼
        WebView2        CSV Import    Providers       JSONL Logs
        Sessions             │             │
              │              └──────┬──────┘
              │                     │
              └──────────────┬──────┘
                             ▼
                       Proxy Pool
                             │
                 ┌───────────┼───────────┐
                 ▼           ▼           ▼
              Proxy A     Proxy B     Proxy C
                 │           │           │
                 ▼           ▼           ▼
             Session 1   Session 2   Session 3
```

---

# 🔁 Session Lifecycle

```text
START
  │
  ▼
Configure Target
  │
  ▼
Configure Sessions
  │
  ▼
Load Proxy Sources
  │
  ▼
Normalize Proxies
  │
  ▼
Health Checks
  │
  ▼
Build Proxy Pool
  │
  ▼
Reserve Proxy
  │
  ▼
Create WebView2 Environment
  │
  ▼
Start Session
  │
  ▼
Monitor
  │
 ┌┴──────────────┐
 ▼               ▼
Healthy        Failure
 │               │
 │               ▼
 │         Mark Proxy Failed
 │               │
 │               ▼
 │        Acquire Replacement
 │
 ▼
Rotation Interval
 │
 ▼
Rotate Proxy
 │
 ▼
Continue / Stop
```

---

# 🖥️ Dashboard

The WPF dashboard provides centralized visibility into:

* Active sessions
* Proxy pool
* Healthy proxies
* Failed proxies
* Latency
* Session state
* Successful events
* Failed events
* Runtime activity
* Errors

The dashboard acts as the control center for the entire application.

---

# 🪟 Browser Matrix

WebSessionForge provides a browser matrix for inspecting active WebView2 sessions.

Each session can be viewed independently while the main dashboard remains available.

Browser audio is muted for WebView2 sessions.

Closing/hiding the browser matrix does **not** terminate active sessions.

Sessions are terminated using the main:

```text
Stop
```

control.

---

# 📊 Observability & Logging

WebSessionForge uses **JSON Lines (JSONL)** for runtime logging.

This allows individual events to be processed independently.

## Application Logs

```text
logs/application-YYYY-MM-DD.jsonl
```

## Session Logs

```text
logs/session-1-YYYY-MM-DD.jsonl
logs/session-2-YYYY-MM-DD.jsonl
logs/session-3-YYYY-MM-DD.jsonl
```

Events can include:

* Session start
* Session stop
* Proxy assignment
* Proxy rotation
* Retry
* Failure
* Proxy health
* Health-check progress
* Health-check summaries
* Runtime errors

Example:

```json
{
  "timestamp": "2026-10-02T12:00:00Z",
  "event": "ProxyHealthSummary",
  "healthy": 12,
  "dead": 4,
  "total": 16
}
```

> Example format only. Actual emitted fields may vary by implementation.

---

# ⚙️ Configuration

Default configuration:

```text
config/config.json
```

Example:

```json
{
  "SessionCount": 6,
  "RotationMinutes": 5,
  "RotationJitterSeconds": 30,
  "ProxyHealthTimeoutSeconds": 10,
  "ProviderRefreshMinutes": 5,
  "ProviderUrls": [],
  "Headless": false
}
```

## Configuration Reference

| Setting                     | Purpose                        |
| --------------------------- | ------------------------------ |
| `SessionCount`              | Number of browser sessions     |
| `RotationMinutes`           | Proxy rotation interval        |
| `RotationJitterSeconds`     | Rotation timing variation      |
| `ProxyHealthTimeoutSeconds` | Health-check timeout           |
| `ProviderRefreshMinutes`    | Provider refresh interval      |
| `ProviderUrls`              | Proxy provider endpoints       |
| `Headless`                  | Headless session configuration |

> The current UI limits the rotation interval input to **1–5 minutes**.

---

# 🧩 Technology Stack

| Layer          | Technology              |
| -------------- | ----------------------- |
| Language       | C#                      |
| Framework      | .NET 8                  |
| UI             | WPF                     |
| Browser Engine | Microsoft Edge WebView2 |
| Configuration  | JSON                    |
| Proxy Input    | CSV / Provider APIs     |
| Logging        | JSONL                   |
| Build          | .NET SDK / MSBuild      |
| Platform       | Windows                 |

---

# 📁 Project Structure

```text
WebSessionForge/
│
├── Models/
│   └── Application models
│
├── Providers/
│   └── Proxy providers/importers
│
├── Services/
│   ├── Session management
│   ├── Proxy management
│   ├── Health checking
│   └── Logging
│
├── config/
│   └── config.json
│
├── logs/
│   └── Runtime JSONL logs
│
├── App.xaml
├── App.xaml.cs
│
├── MainWindow.xaml
├── MainWindow.xaml.cs
│
├── BrowserWindow.xaml
├── BrowserWindow.xaml.cs
│
├── WebSessionForge.csproj
├── DOCUMENTATION.md
├── README.md
├── LICENSE
└── .gitignore
```

---

# 🛠️ Requirements

## Operating System

* Windows 10
* Windows 11

## Runtime

* Microsoft Edge WebView2 Runtime

## Development

* .NET 8 SDK
* Windows development environment
* WebView2 Runtime

---

# 🚀 Installation

## Clone the Repository

```bash
git clone https://github.com/shagarithvik/WebSessionForge.git
```

Enter the project:

```bash
cd WebSessionForge
```

Restore dependencies:

```bash
dotnet restore
```

Build:

```bash
dotnet build
```

Run:

```bash
dotnet run
```

---

# 📦 Publish a Windows Build

Create a self-contained Windows executable:

```bash
dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true -p:DebugType=None
```

Output:

```text
bin/Release/net8.0-windows/win-x64/publish/
```

---

# 🧪 Typical QA Workflow

### 1. Configure Target

Provide the authorized test URL.

```text
https://example.com
```

### 2. Configure Sessions

Choose the required number of isolated sessions.

### 3. Load Proxy Sources

Import a CSV file or configure provider URLs.

### 4. Validate Proxies

Run health checks and inspect:

* Healthy count
* Failed count
* Latency
* Proxy availability

### 5. Start Sessions

Launch the configured WebView2 sessions.

### 6. Inspect

Open the browser matrix to inspect individual sessions.

### 7. Monitor

Observe:

* Session state
* Proxy assignment
* Latency
* Failures
* Recovery
* Runtime logs

### 8. Stop

Terminate the active test run.

### 9. Analyze Logs

Inspect the generated JSONL files for session-level events.

---

# 🔄 Failure Recovery

When a proxy becomes unavailable:

```text
Session
   │
   ▼
Network Failure
   │
   ▼
Proxy Marked Failed
   │
   ▼
Proxy Removed From Assignment
   │
   ▼
Replacement Requested
   │
   ▼
New Proxy Assigned
   │
   ▼
Session Recovery
```

This allows individual proxy failures to be handled without necessarily terminating the entire test run.

---

# 🔬 Testing Use Cases

WebSessionForge can be used for controlled testing of:

### Session Isolation

Verify that browser state remains separated between sessions.

### Proxy Reliability

Observe application behavior when network proxies become unavailable.

### Proxy Rotation

Test application behavior under changing network endpoints.

### Failure Recovery

Verify that sessions can recover after network failures.

### Provider Reliability

Test behavior when proxy providers return malformed, unavailable, or incomplete data.

### Browser Automation

Experiment with multiple independent browser environments.

### Observability

Reconstruct session activity from structured JSONL logs.

---

# 📈 Performance

Multiple WebView2 sessions can consume significant system resources.

Primary resource consumers include:

```text
WebView2
Browser Rendering
JavaScript
Network Connections
Browser Profiles
Session State
```

Performance depends on:

* Number of sessions
* Target page complexity
* JavaScript workload
* Proxy latency
* CPU
* RAM
* Network bandwidth

Start with a small number of sessions and scale gradually.

---

# 🧱 Design Principles

### Isolation

Keep browser sessions independent.

### Observability

Make session and proxy behavior visible.

### Fault Tolerance

Expect network infrastructure to fail.

### Modularity

Separate:

```text
Models
Providers
Services
UI
Configuration
Logging
```

### Replaceability

Allow failed network assignments to be replaced where possible.

### Testability

Build components so individual behaviors can be tested independently.

---

# 🛣️ Roadmap

* [ ] MVVM architecture refinement
* [ ] Dependency injection
* [ ] Stronger configuration validation
* [ ] Proxy-provider health scoring
* [ ] Advanced session telemetry
* [ ] Configurable browser profiles
* [ ] Improved WebView2 lifecycle management
* [ ] Better error categorization
* [ ] Structured run/session IDs
* [ ] Exportable test reports
* [ ] CSV validation UI
* [ ] Proxy pool statistics
* [ ] Automated unit tests
* [ ] Integration tests
* [ ] CI/CD pipeline
* [ ] Release automation
* [ ] Application update mechanism
* [ ] Mock proxy provider
* [ ] Local development proxy simulator

---

# 🤝 Contributing

Contributions are welcome around:

* Browser-session infrastructure
* QA tooling
* Reliability
* Proxy management
* Observability
* Performance
* Testing
* Developer experience

### Development

```bash
git clone https://github.com/shagarithvik/WebSessionForge.git

cd WebSessionForge

git checkout -b feature/my-improvement

dotnet restore

dotnet build

dotnet run
```

Before submitting a pull request, include:

* Description
* Motivation
* Testing performed
* Screenshots where useful
* Configuration changes
* Any compatibility considerations

---

# 🐛 Reporting Issues

When opening an issue, provide:

```text
OS:
.NET version:
WebView2 version:
Session count:
Proxy type:
Target environment:
Error message:
Relevant log event:
Steps to reproduce:
```

Do not include passwords, API keys, authentication tokens, or private proxy credentials.

---

# ⚠️ Responsible Use

WebSessionForge is intended for **authorized testing and experimentation**.

Use it only with:

* Systems you own
* Systems you have permission to test
* Proxies you are authorized to use
* Accounts you are authorized to automate
* Targets whose Terms of Service permit your testing

Do not use the software to:

* Generate artificial engagement
* Manipulate platform metrics
* Circumvent access controls
* Evade anti-bot systems
* Bypass rate limits
* Access systems without authorization
* Abuse third-party infrastructure

The project does not claim that automated traffic constitutes genuine organic user activity.

---

# 📚 Documentation

Additional technical documentation:

```text
DOCUMENTATION.md
```

The documentation covers:

* Architecture
* Configuration
* Proxy management
* CSV formats
* Logging
* Build instructions
* Troubleshooting
* Session management

---

# 📜 License

This project is licensed under the **MIT License**.

See:

```text
LICENSE
```

The MIT License permits use, modification, distribution, and commercial use subject to the license terms.

---

# 👨‍💻 Author

## Rithvik Shaga

Computer Science & Cybersecurity

GitHub:

https://github.com/shagarithvik

---

# ⭐ WebSessionForge

If you find the project useful, consider giving it a ⭐ on GitHub.

Repository:

**https://github.com/shagarithvik/WebSessionForge**

---

## ⚡ Quick Reference

### Clone

```bash
git clone https://github.com/shagarithvik/WebSessionForge.git
```

### Build

```bash
dotnet restore
dotnet build
```

### Run

```bash
dotnet run
```

### Publish

```bash
dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true -p:DebugType=None
```

### Configuration

```text
config/config.json
```

### Logs

```text
logs/
```

### Proxy Schemes

```text
HTTP
HTTPS
SOCKS4
SOCKS5
```

### Platforms

```text
Windows 10
Windows 11
```

### Runtime

```text
.NET 8
Microsoft Edge WebView2 Runtime
```

---

## 🧭 In One Sentence

> **WebSessionForge is a .NET 8 WPF platform for isolated WebView2 browser sessions, proxy management, health monitoring, rotation, recovery, and runtime observability.**

---

### Built with C# • .NET 8 • WPF • WebView2
