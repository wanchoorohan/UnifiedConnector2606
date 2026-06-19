## Getting Started

### Installation and Setup

**Download and import the module:**
1. Go to the Mendix Marketplace and download Teamcenter Connector 2606. Remember, it's now a single module—you don't need to download separate components.
2. Import it into your Mendix 11 app (right-click on your project in the App Explorer and choose "Import module package").

**Add required dependencies:**
3. Make sure your app includes Community Commons 11.4 or higher and the Encryption module. These are both available from the Marketplace.

**Configure security:**
4. Assign the `TcConnector.User` role to the relevant user roles in your app's security settings. **Important**: The Administrator role now only has access to Teamcenter Configuration. For all integration work, use the User role.
5. Click "Update Security" in Studio Pro to refresh entity access rules.

**Set up navigation:**
6. Add Teamcenter Connector navigation items to your app's navigation menu or home page so users can access the integration features.

**Enable React client:**
7. Make sure the React client is enabled in your app settings (Project → Settings → Runtime). This is required for the Web Extension UI to work properly.

### Connecting to Teamcenter

Before you can build integrations, you need to establish a connection to your Teamcenter environment.

**Create a Teamcenter Service Document:**
1. In the App Explorer, right-click on the module where you want to create your integrations
2. Select "Add document" → "Teamcenter Service Document"
3. Give it a name (e.g., "PartsIntegrations" or "MainTeamcenterConnection")

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
A Teamcenter Service Document as a container that holds everything related to your Teamcenter integrations within a specific module. 

In previous versions, the Extension stored configuration files on disk, separate from your Mendix app. This made version control tricky and team collaboration more complex. With the Service Document approach, everything is stored inside your Mendix module—just like microflows, pages, and domain models. This means:
- Your integrations travel with your module when you export it or commit it to version control
- Team members automatically get the latest integration configurations when they update from version control
- You can have different Service Documents for different purposes within the same app

**What a Service Document contains:**

1. **Settings** — How to reach Teamcenter and how to authenticate
  - This is where you configure the connection to Teamcenter
  - Set up your authentication method, test the connection, and manage sign-in state
  - You typically configure this once per Service Document, unless you need to switch environments
2. **Integrations (journeys)** — All the configured integration patterns you've set up with option of adding a new one
  - It shows an overview of all the integrations you've configured in this document
  - You can see the journey type, the entities and microflows that were generated, and when each integration was last modified
  - From here, you can:
    - View details of an integration
    - Edit an existing integration
    - Duplicate an integration (useful for creating variations)
    - Delete integrations you no longer need
    - Navigate directly to the generated microflows and entities by clicking on them
  - It also allows you to add new integrations      

**You can have multiple Service Documents:**
You can create several Service Documents in a single app, each in its own module. This is useful for separating concerns. For example:
- A "Parts" module with a Service Document for part-related integrations
- A "Workflows" module with a Service Document for workflow-related integrations
- A "Documents" module with a Service Document for dataset and document integrations


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
10. For more details on Settings tab, refer to Settings in the old Teamcenter Extension - https://docs.mendix.com/appstore/modules/siemens-plm/teamcenter-extension/create-an-integration/#settings-tab
11. Click "Sign In" to test the connection

**Step 3: Start your first journey**
12. Click on the "Integrations" tab at the top. Right now, it's empty—this is normal for a new Service Document
15. Click on "Add Integration"
16. You'll see tiles representing the 11 available journey types (like "Search Item Revisions," "Create Item with Item Revision," etc.)
16. Click on one of these tiles to start configuring that type of integration
17. This will take you to the Import Mapping page

**Step 4: Use the Import Mapping Mapping Page**
The Import Mapping page is split into two sides:

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
