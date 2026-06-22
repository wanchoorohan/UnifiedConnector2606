# Teamcenter Connector 2606 — Documentation Guide

---

## Overview & Introduction

### What is Teamcenter Connector 2606?

Teamcenter Connector 2606 marks a shift in how Mendix integrates with Teamcenter. For the first time, we're bringing together everything you need in a single, unified Marketplace module. This release combines what were previously two separate Marketplace downloads: Teamcenter Connector and the Teamcenter Extension (which itself contained two modules: Teamcenter Toolkit and the Extension add-on) into one streamlined package that works seamlessly with Mendix 11.12 and higher.

Additionally, Teamcenter Extension has been re-built using the Web Extensibility Framework and is now available as a service document. You can think of a Service Document as a container that can be created on a per module basis and holds everything related to your Teamcenter integrations within a specific module. 

In previous versions, the Extension stored configuration files on disk, separate from your Mendix app. This made version control tricky and team collaboration more complex. With the Service Document approach, everything is stored inside your Mendix module, just like microflows, pages, and domain models. This means:
- Your integrations travel with your module when you export it or commit it to version control
- Team members automatically get the latest integration configurations when they update from version control
- You can have different Service Documents for different purposes within the same app
  
### Architecture Overview

Here's how all the pieces fit together:

```
Studio Pro (Mendix 11.12+)
│
├── Teamcenter Service Document  ←  stores integrations + connection settings
│     └── Extension UI (TypeScript / Web Extensibility Framework)
│           ├── Import Mapping Canvas
│           ├── Journey Configuration Dialogs
│           ├── Integrations Tab
│           └── Settings Tab
│
├── Generated Artifacts (per module)
│     ├── Domain Model  (entities, associations, specializations)
│     └── Microflows    (search, create, update, revise, get, relate, BOM)
│
└── TcConnector module (runtime — includes former Toolkit)
      ├── Java Actions / Toolbox Services  [Published — Teamcenter icon]
      ├── Internal Actions                 [Private — no icon]
      ├── TcConnector Domain Model (base entities, BOMLine, search criteria)
      ├── CallTeamcenterService (custom SOA calls)
      └── Authentication (Credentials / TcSSO / TcX SSO via HttpListener)
              │
              ▼
        Teamcenter SOA Layer
        (self-hosted v2406+ / Teamcenter X v2506+)
```

**How it works in practice:**

1. You start in the Teamcenter Service Document, where you configure your connection and create integrations using the visual journey editors.
2. The Extension generates domain model entities and microflows specific to your integration needs, placing them directly in your module.
3. At runtime, your microflows use the TcConnector's Java actions to communicate with Teamcenter's SOA layer, using the authentication method you configured.

### Prerequisites and Compatibility

Before you start working with Teamcenter Connector 2606, make sure your environment meets these requirements:

| Requirement | Minimum version |
|---|---|
| Mendix Studio Pro | 11.12.0 |
| Teamcenter (self-hosted) | 2606, 2512, 2506 |
| Teamcenter X | 2606, 2512, 2506 |

**Required Marketplace modules:**
- Community Commons 11.4+
- Encryption
