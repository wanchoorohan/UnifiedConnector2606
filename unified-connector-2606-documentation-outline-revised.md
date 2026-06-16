# Teamcenter Connector 2606 — Documentation Guide

---

## Overview & Introduction

### What is Teamcenter Connector 2606?

Teamcenter Connector 2606 marks a significant milestone in how Mendix integrates with Teamcenter. For the first time, we're bringing together everything you need in a single, unified Marketplace module. This release combines what were previously two separate Marketplace downloads—the Teamcenter Connector and the Teamcenter Extension (which itself contained two modules: Teamcenter Toolkit and the Extension add-on)—into one streamlined package that works seamlessly with Mendix 11 and higher.

Whether you're building your first Teamcenter integration or migrating an existing application, this connector enables you to work with Teamcenter product data directly from your Mendix apps—without needing to become a Teamcenter SOA expert.

The unified module provides two complementary layers that work together:

- **Connector layer** — This is your runtime engine. It provides Java actions, a ready-to-use domain model, and all the service infrastructure needed to communicate with Teamcenter's SOA layer. Think of this as the foundation that handles the technical communication with Teamcenter.

- **Extension layer** — This is your design-time companion, built directly into Studio Pro. It guides you through configuring integrations using a visual interface, then automatically generates the microflows and domain model artifacts you need. Instead of writing integration code from scratch, you configure what you want and let the Extension build it for you.

### What Changed from Previous Versions?

This release represents both a consolidation and a modernization of the Teamcenter connector ecosystem. Here's what's different and why it matters:

#### Module-level changes

**Everything in one place** — The biggest change you'll notice is that Teamcenter Connector 2606 now delivers everything as a single module. Previously, you had to download two separate items from the Marketplace: the Teamcenter Connector module and the Teamcenter Extension (which contained two modules—Teamcenter Toolkit and the Extension add-on). Now it's one download, one import, and one version to track.

**Teamcenter Toolkit has been integrated** — The Toolkit module no longer exists as a standalone component. All of its microflows, entities, and Java actions are now part of the TcConnector module, using the same names you're familiar with. **Important**: This is a breaking change if you have existing apps that reference Toolkit module elements—you'll need to update those references during migration.

**Mendix 11 is now required** — Version 2606 requires Mendix Studio Pro 11.12 or higher. If you're still on Mendix 10, you'll need to upgrade your Studio Pro version before adopting this release.

#### Connector layer changes

**Security has been hardened** — We've removed Create and Delete rights from all entities to align with security best practices. The Administrator role no longer has direct entity-level access (except for Teamcenter Configuration). For all Teamcenter integrations, you should now use the User role. This is a breaking change that improves security but may require updates to your existing security configuration.

**Clearer public and private boundaries** — The Connector now has a well-defined public API. Actions marked with a Teamcenter icon in Studio Pro are published and intended for your use. Actions without the icon are internal implementation details and shouldn't be called directly from your microflows.

**Deprecations with clear replacements** — Several older microflows have been deprecated, but each has a direct replacement. We've documented these in the migration guide so you know exactly what to use instead.

**Login and Logout behavior refined** — The `Login` Java action no longer returns a Boolean—it either succeeds or throws an exception, making error handling more predictable. The `Logout` action now returns a Boolean to indicate success. The older `ExecuteLogin` and `ExecuteLogout` microflows have been deprecated in favor of the direct `Login` and `Logout` microflows.

**FileType is now NamedReference** — For consistency with how the Extension layer names things, we've renamed `FileType` and `File Type` to `NamedReference` and `Named Reference` throughout entities and microflows. This is a breaking change—you'll need to update references in your existing microflows.

**CreateBOMWindow improvements** — The `CreateBOMWindow_Generic` microflow has been updated: its parameters are now optional, and we've split off a `ByConfigurationContext` variant for cases where you need that level of control. The pre-configured variants have been removed to simplify the API surface.

**ConfigurationName is always required** — The `ModelObject` Java class now consistently requires `ConfigurationName` to be set in all cases. Previously, the behavior varied depending on whether you passed a configuration name or not—this inconsistency has been resolved.

**Better error handling** — The Connector no longer shows in-app error messages automatically. Instead, all error conditions throw exceptions, giving your microflows full control over how to handle and present errors to users. This makes error handling more predictable and testable.

**SSO improvements** — We've enhanced error logging around SSO configuration to make troubleshooting easier. Teamcenter SSO is now compatible with Teamcenter 2406 (previously required 2512 or higher). User provisioning has also changed: the `TcSSOUserInformation` entity now only contains `User` and `Locale`—use the `User` attribute (not `sub`) to get the Teamcenter username.

**BOMLine attributes updated** — Attribute lengths have been updated to match Teamcenter's default values, reducing the chance of data truncation issues.

**ObjectHandling module removed** — The ObjectHandling module is no longer a dependency. You can safely remove it from existing projects during migration.

**More flexible URL handling** — Teamcenter URLs without a port number, without a trailing slash, or without a top-level domain now work correctly. This makes configuration more forgiving.

#### Extension layer changes

**Built on modern technology** — The Extension is now built using the Mendix Web Extensibility Framework (TypeScript). Previously, it used the C# Extensibility Framework, which Mendix is discontinuing. This modernization ensures the Extension will continue to evolve with future Mendix releases.

**Teamcenter Service Document introduced** — Integrations and connection settings are now stored as a document within your module, just like microflows and pages. This replaces the previous menu-based Extension that stored configuration files on disk. The advantage? Your integrations now travel with your module in version control, making team collaboration and deployment much smoother.

**Redesigned to match Studio Pro** — The Extension UI has been redesigned to follow the Studio Pro Design System, so it looks and feels like a native part of Studio Pro. The experience is more consistent with the rest of your development environment.

**SSO uses internal HttpListener** — Both Teamcenter SSO and Teamcenter X SSO now use the internal extensibility API listener instead of requiring the OIDC module. There's a unified callback URL, replacing the separate callback port setting you had to configure before.

**Input entity naming convention updated** — CreateInput and CompoundCreateInput entities now follow a new naming convention that aligns with TcConnector standards. This is a breaking change: if you have existing journeys, you'll need to regenerate them using the new Extension.

**Mac OS compatibility** — The Extension now works seamlessly on Mac OS, expanding the platforms you can use for development.

### Key Features and Capabilities

#### Connector Layer (runtime)

The Connector layer is where the work happens at runtime. Here's what it enables:

**Access Teamcenter business objects** — A rich set of included Java actions (often called "toolbox services") gives you immediate access to common Teamcenter operations like searching, creating, updating, and retrieving data.

**Call any Teamcenter SOA service** — For operations not covered by the included actions, you can call any Teamcenter SOA service directly using `CallTeamcenterService` (Java action) or `TcConnection.callTeamcenterService` (Java method). You just need to provide the operation mapping as JSON.

**Multiple Teamcenter instances** — The Connector supports multiple active Teamcenter configurations simultaneously (multi-instance), so you can work with different Teamcenter environments from the same app.

**Three authentication modes** — Choose the authentication method that fits your environment:
  - **Credentials** — Username and password, ideal for development and non-SSO environments
  - **Teamcenter SSO** — For on-premises installations using Teamcenter Security Services
  - **Teamcenter X SSO** — For cloud-hosted Teamcenter X environments

**Extensible domain model** — You can specialize the built-in entities to map your custom Teamcenter business objects, giving you flexibility while maintaining the integration structure.

**Structured error handling** — All errors are thrown as exceptions rather than being handled automatically. This means your microflows decide how to handle errors, making your application's behavior more predictable.

**Optional FMS URL** — File Management System (FMS) operations are available when you configure the FMS URL, but the connector works perfectly fine without it if you don't need file operations.

**Enhanced WhereUsed queries** — WhereUsed2 now includes `clientId` and `level` properties, giving you richer query capabilities for where-used operations.

#### Extension Layer (design-time)

The Extension layer accelerates your development by automating the repetitive parts of building Teamcenter integrations:

**Teamcenter Service Document** — This document-based approach stores your integrations at the module level. You can create multiple Service Documents per app, which is useful for organizing integrations by functional area (for example, a Parts module and a Workflows module can each have their own Service Document).

**Visual import mapping canvas** — The mapping interface is modeled after Studio Pro's Import Mapping editor, so if you're familiar with that, you'll feel right at home. It shows the Teamcenter object hierarchy on one side and your Mendix entities on the other, making it easy to see what you're mapping.

**11 guided journey types** — Journeys are pre-configured integration patterns that walk you through the setup process. Each journey generates the microflows and domain model entities you need:
  - Search Item Revisions
  - Create Item with Item Revision
  - Update Item with Item Revision
  - Revise Item Revision
  - Search Datasets
  - Get Datasets for Item Revision
  - Attach Dataset to Item Revision
  - Search Workspace Objects
  - Relate Workspace Objects
  - Get Properties
  - Get Structure (BOM)

**Automatic code generation** — Once you configure a journey, the Extension generates everything for you: entities, associations, create/revise input entities, and the microflows that use them. Entities are even positioned as a tree in your domain model for easy visualization.

**History tab** — This gives you an overview of all the integrations you've configured in a Service Document. You can view, edit, duplicate, and delete integrations, and you can navigate directly to the generated microflows and entities with a single click.

**Built-in consistency checks** — The Extension validates your configuration before generating code, checking entities, attributes, associations, and microflows. It distinguishes between errors that can be automatically fixed and those that need your attention.

**Progress dialogs** — For operations that take a while (like generating multiple entities and microflows), the Extension shows progress dialogs so you know what's happening.

**Multi-language support** — The Extension UI follows your Studio Pro language preference, so it works in the language you're most comfortable with.

**Cross-platform support** — Works on both Mac OS and Windows, so your entire team can use it regardless of their development platform.

### Architecture Overview

Here's how all the pieces fit together:

```
Studio Pro (Mendix 11.12+)
│
├── Teamcenter Service Document  ←  stores integrations + connection settings
│     └── Extension UI (TypeScript / Web Extensibility Framework)
│           ├── Import Mapping Canvas
│           ├── Journey Configuration Dialogs
│           ├── History Tab
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
| Teamcenter (self-hosted) | 2406 |
| Teamcenter Security Services (self-hosted) | 2406 |
| Teamcenter X | 2506 |

**Required Marketplace dependencies:**
- Community Commons 11.4+
- Encryption

**No longer required (you can remove these when upgrading):**
- OIDC SSO module — authentication is now handled internally
- User Commons — no longer needed
- Mx Model Reflection — no longer needed
- ObjectHandling module — no longer a dependency
- Teamcenter Toolkit module — now integrated into TcConnector

**Compatibility matrix** — For a detailed compatibility matrix covering all supported versions, see *(link to updated compatibility-matrix.md)*

---

## Getting Started

### Installation and Setup

Getting Teamcenter Connector 2606 up and running is straightforward. Here's the process:

**Download and import the module:**
1. Go to the Mendix Marketplace and download Teamcenter Connector 2606. Remember, it's now a single module—you don't need to download separate components.
2. Import it into your Mendix 11 app (right-click on your project in the App Explorer and choose "Import module package").

**Add required dependencies:**
3. Make sure your app includes Community Commons 11.4 or higher and the Encryption module. These are both available from the Marketplace.

**Clean up obsolete modules:**
4. If you're upgrading from an earlier version, remove these modules that are no longer needed:
   - OIDC SSO
   - User Commons
   - Mx Model Reflection
   - ObjectHandling
   - Teamcenter Toolkit (now merged into TcConnector)

**Configure security:**
5. Assign the `TcConnector.User` role to the relevant user roles in your app's security settings. **Important**: The Administrator role now only has access to Teamcenter Configuration. For all integration work, use the User role.
6. Click "Update Security" in Studio Pro to refresh entity access rules.

**Set up navigation:**
7. Add Teamcenter Connector navigation items to your app's navigation menu or home page so users can access the integration features.

**Enable React client:**
8. Make sure the React client is enabled in your app settings (Project → Settings → Runtime). This is required for the Web Extension UI to work properly.

**What you've installed:**
Once complete, you'll have:
- The TcConnector module with all Java actions (toolbox services)
- A base domain model with common Teamcenter entities like BOMLine and search criteria
- The ability to create Teamcenter Service Documents for configuring integrations
- All the runtime infrastructure for connecting to Teamcenter

### Connecting to Teamcenter

Before you can build integrations, you need to establish a connection to your Teamcenter environment. Here's how:

**Create a Teamcenter Service Document:**
1. In the App Explorer, right-click on the module where you want to create your integrations
2. Select "Add document" → "Teamcenter Service Document"
3. Give it a meaningful name (e.g., "PartsIntegrations" or "MainTeamcenterConnection")

**Open the Settings tab:**
4. Double-click the Service Document you just created
5. Navigate to the Settings tab (it's one of the tabs at the top of the document)

**Configure your connection:**
The Settings tab is where you tell the connector how to reach Teamcenter and how to authenticate. You'll need to provide:
- The Teamcenter URL (your Teamcenter server address)
- An authentication method (see below for the three options)

**Choose your authentication method:**

**Option 1: Credentials** (simplest, good for development)
- Choose this if you're in a development environment or if your Teamcenter instance doesn't use SSO
- Simply provide a username and password
- Good for getting started quickly and for testing

**Option 2: Teamcenter SSO** (for on-premises installations)
- Choose this if you're using Teamcenter Security Services in an on-premises environment
- You'll need to provide:
  - SSO Login Server URL
  - Identity Server URL
  - TC App ID
  - Callback URL (now simplified—uses the internal HttpListener)
- This method uses the internal extensibility API listener, so you don't need to configure the OIDC module anymore

**Option 3: Teamcenter X SSO** (for cloud deployments)
- Choose this if you're using cloud-hosted Teamcenter X
- You'll need to provide:
  - TcX Client ID
  - SAM Auth client ID and secret
  - Token exchange configuration
  - Callback URL

**Test your connection:**
6. Once you've entered your connection details, click the "Sign In" button to test the connection
7. If the connection succeeds, you'll see a confirmation. If it fails, the Extension will show error details to help you troubleshoot

**Important note about auto-logout:**
The Extension automatically clears your sign-in state when you edit or reset connection details. This is a security feature to ensure you're always using the correct credentials with the correct Teamcenter environment.

**Need more help with SSO?**
For detailed SSO configuration guides covering both on-premises and Teamcenter X scenarios, see *(link to detailed SSO configuration guide)*.

### What is a Teamcenter Service Document?

Think of a Teamcenter Service Document as a container that holds everything related to your Teamcenter integrations within a specific module. It's a new concept introduced in version 2606, and it fundamentally changes how you work with the Teamcenter Extension.

**Why it matters:**
In previous versions, the Extension stored configuration files on disk, separate from your Mendix app. This made version control tricky and team collaboration more complex. With the Service Document approach, everything is stored inside your Mendix module—just like microflows, pages, and domain models. This means:
- Your integrations travel with your module when you export it or commit it to version control
- Team members automatically get the latest integration configurations when they update from version control
- You can have different Service Documents for different purposes within the same app

**What a Service Document contains:**

1. **Connection settings** — How to reach Teamcenter and how to authenticate
2. **Integrations (journeys)** — All the configured integration patterns you've set up

**You can have multiple Service Documents:**
It's perfectly fine to create several Service Documents in a single app, each in its own module. This is useful for separating concerns. For example:
- A "Parts" module with a Service Document for part-related integrations
- A "Workflows" module with a Service Document for workflow-related integrations
- A "Documents" module with a Service Document for dataset and document integrations

**Understanding the tabs:**

When you open a Service Document, you'll see three main tabs:

**History tab** (your integration dashboard)
- This is the default landing page when you open a Service Document
- It shows an overview of all the integrations you've configured in this document
- You can see the journey type, the entities and microflows that were generated, and when each integration was last modified
- From here, you can:
  - View details of an integration
  - Edit an existing integration
  - Duplicate an integration (useful for creating variations)
  - Delete integrations you no longer need
  - Navigate directly to the generated microflows and entities by clicking on them

**Import Mapping tab** (where you build integrations)
- This is the visual canvas where you configure new integrations or edit existing ones
- It's modeled after Studio Pro's Import Mapping editor, so the interaction pattern should feel familiar
- You'll use this tab to map Teamcenter objects to Mendix entities and configure journey-specific settings

**Settings tab** (connection configuration)
- This is where you configure the connection to Teamcenter
- Set up your authentication method, test the connection, and manage sign-in state
- You typically configure this once per Service Document, unless you need to switch environments

### Creating Your First Service Document

Let's walk through creating your first Service Document step by step. This is where your Teamcenter integration journey begins.

**Step 1: Create the document**
1. In Studio Pro's App Explorer, find the module where you want to build your Teamcenter integration
2. Right-click on the module name
3. Select "Add document" → "Teamcenter Service Document"
4. Give it a descriptive name, like "TeamcenterMain" or "PartsIntegration"
5. Press Enter or click OK

**Step 2: Configure your connection**
6. Double-click on the Service Document you just created to open it
7. Click on the "Settings" tab at the top
8. Enter your Teamcenter URL (for example, `https://yourserver.example.com/tc`)
9. Choose your authentication method and fill in the required fields:
   - For Credentials: enter username and password
   - For Teamcenter SSO: enter SSO Login Server URL, Identity Server URL, TC App ID, and callback URL
   - For Teamcenter X SSO: enter TcX Client ID, SAM Auth details, and callback URL
10. Click "Sign In" to test the connection
11. If the sign-in succeeds, you're ready to create integrations. If it fails, review the error message and check your connection details

**Step 3: Explore the History tab**
12. Click on the "History" tab at the top
13. Right now, it's empty—this is normal for a new Service Document
14. This tab will fill up as you create integrations. Each integration will appear here as a card showing:
    - The journey type (e.g., "Search Item Revisions")
    - The integration name
    - The generated microflows and entities
    - When it was last modified

**Step 4: Start your first journey**
15. From the History tab, you'll see tiles representing the 11 available journey types (like "Search Item Revisions," "Create Item with Item Revision," etc.)
16. Click on one of these tiles to start configuring that type of integration
17. This will take you to the Import Mapping canvas

**Step 5: Use the Import Mapping canvas**
The Import Mapping canvas is split into two sides:

**Teamcenter side (left):**
- Browse the Teamcenter object hierarchy
- Select the business object type you want to work with (for example, "Item Revision" or "Dataset")
- The Extension connects to your Teamcenter instance to show you the available types

**Mendix side (right):**
- Choose whether to create a new entity or use an existing one
- If creating new, the Extension will generate a specialized entity for you
- If using existing, you can select an entity you've already defined in your domain model

**Property mapping (center):**
- Once you've selected the Teamcenter object type and Mendix entity, you'll see a list of properties
- Use the checkboxes to indicate:
  - "Read" — should this property be retrieved from Teamcenter?
  - "Write" — should this property be sent to Teamcenter when creating or updating?
- Select the properties you need for your integration

**Journey-specific panels:**
- Depending on the journey type, you'll see additional configuration panels
- For example:
  - Search journeys let you configure search criteria
  - Dataset journeys let you filter by dataset type
  - BOM journeys let you configure BOM window properties and revision rules
- Fill in these journey-specific settings as needed

**Step 6: Generate your integration**
18. Once you've configured everything, click the "Generate" button
19. The Extension will:
    - Validate your configuration to make sure everything is correct
    - Generate domain model entities (placed in your domain model as a tree for easy visualization)
    - Generate microflows that implement the integration logic
    - Save the integration configuration to the History tab
20. If generation takes a while (for complex integrations), you'll see a progress dialog showing what's happening

**Step 7: Explore the results**
21. After generation completes, you'll automatically return to the History tab
22. Find your newly created integration in the list
23. Click on it to expand the details
24. You'll see links to:
    - The generated microflows (click to jump directly to them in Studio Pro)
    - The generated entities (click to jump directly to the domain model)
25. Try clicking on a microflow name—Studio Pro will open that microflow, and you can see exactly what was generated

**Step 8: Run your integration**
26. To test your integration, add a call to the generated microflow from a page or another microflow in your app
27. Run your app locally and trigger the integration
28. If you run into issues, the generated microflows have built-in error handling that throws exceptions—you can catch these in your calling microflow to handle errors gracefully

**What you've learned:**
You now know how to:
- Create a Service Document
- Configure a connection to Teamcenter
- Start a journey and use the Import Mapping canvas
- Generate microflows and entities
- Find and explore the generated artifacts

### Your First Journey

Now that you understand the mechanics of creating a Service Document, you're ready to dive deeper into specific journey types.

**What are journeys?**
Journeys are guided integration patterns—think of them as templates for common Teamcenter operations. Each journey type is optimized for a specific task, like searching for item revisions, creating new items, or retrieving BOM structures. There are 11 journey types in total, covering the most common integration scenarios.

**Why use journeys?**
Instead of writing integration code from scratch, journeys let you configure what you want through a visual interface. The Extension then generates production-ready microflows and domain model entities that implement your integration. This approach:
- Saves time (no need to learn the intricacies of Teamcenter SOA)
- Reduces errors (the generated code follows best practices)
- Makes maintenance easier (regenerate when your requirements change)

**Where to learn more:**
Each journey type has a detailed step-by-step guide with screenshots showing you exactly how to configure it. These guides walk you through:
- What the journey is for
- What configuration options you have
- What entities and microflows are generated
- How to use the generated artifacts in your app

**Find the journey guides here:** *(link to existing integrations/ guides)*

**A note about screenshots:**
If you're migrating from an earlier version, you might notice the screenshots in the guides look different. This is because the Extension UI has been redesigned to follow the Studio Pro Design System. The steps and functionality are the same—only the visual appearance has changed to make the Extension feel more like a native part of Studio Pro.

---

## Migrating to 2606

### Migrating from Teamcenter Connector 2512 and Extension v4.x

If you have an existing app using Teamcenter Connector 2512 and Extension v4.x, you can migrate to version 2606. While this migration involves some breaking changes, the benefits—unified module, modern Extension framework, improved security, and better error handling—make it worthwhile.

**Before you start: Pre-migration checklist**

⚠️ **Follow these steps carefully to avoid issues:**

1. **Upgrade to Mendix 11.12 or higher first** — Version 2606 requires Mendix 11.12+. If you're on Mendix 10, you must upgrade Studio Pro before importing the new connector. Mendix 10 cannot run 2606.

2. **Have only one developer perform the migration** — If multiple developers migrate simultaneously, you'll end up with duplicate generated artifacts. Coordinate with your team and designate one person to handle the migration, then have others pull the updated code from version control.

3. **Make a backup** — Before starting, either create a full backup of your app or commit all current changes to version control. This gives you a safety net in case you need to roll back.

**Step-by-step migration process**

**Step 1: Prepare your environment**
1. Open your app in Studio Pro 11.12.0 or higher
2. Make sure all changes are committed (if using version control) or backed up

**Step 2: Import the new connector**
3. Go to the Mendix Marketplace and download Teamcenter Connector 2606
4. Import it into your app
5. This will replace your existing Teamcenter Connector module

**Step 3: Remove obsolete modules**
6. The following modules are no longer needed. Remove them from your app:
   - **Teamcenter Toolkit** — This has been merged into TcConnector. All its functionality is now part of the main connector module.
   - **OIDC SSO** — Authentication now uses the internal HttpListener; you don't need this module anymore
   - **User Commons** — No longer a dependency
   - **Mx Model Reflection** — No longer a dependency
   - **ObjectHandling** — No longer a dependency

**Step 4: Resolve breaking changes**
7. Studio Pro will show you errors where your microflows or domain model reference things that have changed. Work through these systematically. The most common issues you'll encounter:
   - References to `TeamcenterToolkit.*` — Change these to `TcConnector.*` (because Toolkit was merged into TcConnector)
   - References to `FileType` — Change these to `NamedReference`
   - Microflows that handle the Boolean return value from `Login` — Remove this handling, as `Login` now throws an exception instead
   - User provisioning microflows using `sub` — Change to use `User` instead
   - See the breaking changes reference table below for a complete list

**Step 5: Update security**
8. Click "Update Security" in Studio Pro (Project → Security → Update Security)
9. Review the User and Administrator role assignments
10. Remember: the Administrator role now only has access to Teamcenter Configuration, so assign the User role to module roles that need entity access

**Step 6: Enable React client**
11. Go to Project → Settings → Runtime
12. Make sure "Enable React client" is turned on (required for the new Web Extension UI)

**Step 7: Create a Teamcenter Service Document**
13. In the module where your Teamcenter integrations live, right-click and select "Add document" → "Teamcenter Service Document"
14. Give it a name (e.g., "MainIntegrations")

**Step 8: Reconfigure your connection**
15. Open the Service Document and go to the Settings tab
16. Re-enter your Teamcenter URL and authentication settings
17. Note: These settings are not migrated automatically—you need to enter them again
18. Click "Sign In" to test the connection

**Step 9: Recreate your integrations**
19. Your existing domain model entities and microflows are still in your app—they haven't been deleted
20. However, you'll want to regenerate them using the new Extension to take advantage of improvements and ensure they're compatible with the new connector
21. In the Service Document, start a journey that matches each of your existing integrations
22. Configure it the same way as before (the journey types and options are the same)
23. Generate the integration
24. You can use the "Duplicate" feature in the History tab to create variations of an integration without reconfiguring from scratch

**Step 10: Test thoroughly**
25. Run each generated microflow against your Teamcenter instance
26. Verify that search, create, update, and retrieval operations work as expected
27. Test error handling by triggering error conditions (e.g., invalid search criteria)
28. If you have automated tests, run them to ensure integration behavior is correct

**Breaking changes reference**

Here's a comprehensive table of breaking changes and what you need to do about each one:

| Area | Change | Action required |
|---|---|---|
| **Teamcenter Toolkit** | Module merged into TcConnector; module no longer exists | Update all references from `TeamcenterToolkit.*` to `TcConnector.*` |
| **Entity security** | Create and Delete rights removed from all entities | Remove any UI or microflow logic that relied on client-side Create/Delete; use microflows instead |
| **Admin role** | Administrator no longer has entity access | Reassign to User role where needed |
| **`FileType` / `File Type`** | Renamed to `NamedReference` / `Named Reference` | Update all references in microflows and mappings |
| **`CreateBOMWindow_Generic`** | Moved to TcConnector; updated to use `CreateOrReconfigureBOMWindows`; pre-configured variants removed | Regenerate BOM microflows via the Extension or update manually to use `TcConnector.CreateBOMWindow_Generic` |
| **`Login` Java action** | No longer returns Boolean; throws exception on failure | Remove Boolean result handling; wrap in error handler |
| **`Logout` Java action** | Now returns Boolean | Update callers if return value was previously ignored |
| **`ExecuteLogin` microflow** | Deprecated; replaced by `Login` microflow | Replace with `Login` |
| **`ExecuteLogout` microflow** | Deprecated; replaced by `Logout` microflow | Replace with `Logout` |
| **`TcSSOUserInformation`** | Now only contains `User` and `Locale`; `sub` removed | Use `User` attribute instead of `sub` for Teamcenter username in user provisioning microflows |
| **Input entity naming** | CreateInput / CompoundCreateInput follow new naming convention | Regenerate affected journeys via the Extension |
| **`ConfigurationName` on ModelObject** | Always required; previously optional in some code paths | Ensure ConfigurationName is always passed |
| **Error messages** | Connector no longer shows in-app messages; throws exceptions instead | Ensure calling microflows have error handlers |

**Deprecated microflows — with replacements**

These microflows still exist but are deprecated. Replace them with the recommended alternatives:

| Deprecated | Replacement |
|---|---|
| `ExecuteLogin` | `Login` microflow |
| `ExecuteLogout` | `Logout` microflow |
| `DownloadFile` | `DownloadFile` / `DownloadImage` Java action |
| `HandleActiveConfigErrors` | No direct replacement (deprecated) |
| `HandleServiceErrors` | No direct replacement (deprecated) |
| `RetrieveConfigNameFromSingleActiveConfiguration` | No direct replacement (deprecated) |
| `RetrieveHttpHeaderList` | No direct replacement (deprecated) |
| `RetrieveTcSessionBasedOnConfigName` | No direct replacement (deprecated) |
| `RetrieveTeamcenterConfigurationByName` | No direct replacement (deprecated) |
| `ShowPartialErrors` | No direct replacement (deprecated) |
| `UpdateSession` | No direct replacement (deprecated) |

**Note about deprecated Java actions:**
Deprecated Java actions remain usable, but they're no longer published (they don't show the Teamcenter icon in Studio Pro). If you still need them, you can manually include them. However, we recommend replacing them with the published alternatives for future compatibility.

**What carries forward automatically**

The good news: not everything needs to be rebuilt from scratch.

✅ **Your domain model is preserved** — Entities and associations generated by the previous Extension remain in your module. They're part of your app, so they don't disappear during the migration.

✅ **Your microflows are preserved** — Generated microflows remain in your app. Only those affected by specific breaking changes (input entity naming, Toolkit module references) need regeneration or updates.

**What requires manual action**

❌ **Connection settings** — You must re-enter these in the Service Document. They're not migrated automatically.

❌ **Toolkit module references** — All references to `TeamcenterToolkit.*` must be changed to `TcConnector.*`

❌ **Deprecated microflow references** — Update calls to deprecated microflows to use their replacements

❌ **FileType references** — Change all `FileType` references to `NamedReference`

❌ **User provisioning logic** — If you're using SSO, update microflows to use the `User` attribute instead of `sub`

❌ **Login Boolean handling** — Remove code that expects `Login` to return a Boolean

### What Happened to the C# Extension?

If you're wondering why the Extension looks and works differently in version 2606, here's the background:

**The old Extension (v1.0.0–v4.x) was built on the C# Extensibility Framework** — This was Mendix's original extensibility framework for Studio Pro, and it served the Teamcenter Extension well for several years.

**Mendix is discontinuing the C# Extensibility Framework** — Active development of the C# framework has ended. Mendix is focusing all future extensibility efforts on the Web Extensibility Framework (TypeScript), which offers better performance, easier maintenance, and cross-platform support.

**The new Extension (2606) is built on the Web Extensibility Framework** — This is Mendix's strategic, actively maintained framework going forward. By rebuilding the Extension on this foundation, we ensure it will continue to evolve with future Mendix releases and receive new features and improvements.

**All capabilities are preserved** — Every journey type and integration capability from the C# Extension is available in the new Extension. You're not losing functionality—just gaining a more modern, maintainable foundation.

**The Teamcenter Service Document model is an improvement** — In addition to the framework change, we've introduced the Service Document approach. This is more flexible and better integrated with Studio Pro than the previous menu-based approach. Your integrations now live inside your modules, making version control and team collaboration smoother.

**What about the old Extension?**
- The C# Extension (v4.x) remains available on the Marketplace for now
- However, it will not receive further updates or new features
- We strongly recommend migrating to 2606 when possible

**What if I'm still on Mendix 10?**
- Apps that cannot yet upgrade to Mendix 11 may continue using Teamcenter Connector 2512 + Extension v4.x in the interim
- However, plan your upgrade path to Mendix 11 and Teamcenter Connector 2606 to ensure you receive future updates and improvements
