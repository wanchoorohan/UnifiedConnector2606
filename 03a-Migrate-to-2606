
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
