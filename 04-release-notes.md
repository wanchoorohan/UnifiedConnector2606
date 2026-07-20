### What Changed from Previous Versions?

#### Module-level changes

**Everything in one place** — The biggest change you'll notice is that Teamcenter Connector 2606 now delivers everything as a single module. Previously, you had to download two separate items from the Marketplace: the Teamcenter Connector module and the Teamcenter Extension (which contained two modules—Teamcenter Toolkit and the Extension add-on). Now it's one download, one import, and one version to track.

**Teamcenter Toolkit has been integrated** — The Toolkit module no longer exists as a standalone component. All of its microflows, entities, and Java actions are now part of the TcConnector module, using the same names you're familiar with. **Important**: This is a breaking change if you have existing apps that reference Toolkit module elements—you'll need to update those references during migration.

**Mendix 11.12 is now required** — Version 2606 requires Mendix Studio Pro 11.12 or higher. If you're still on Mendix 10, you'll need to upgrade your Studio Pro version before adopting this release.

#### Teamcenter Connector changes

**Security has been hardened** — We've removed Create and Delete rights from all entities to align with security best practices. The Administrator role no longer has direct entity-level access (except for Teamcenter Configuration). For all Teamcenter integrations, you should now use the User role. This is a breaking change that improves security but may require updates to your existing security configuration.

**Clearer public and private boundaries** — The Connector now has a well-defined public API. Actions marked with a Teamcenter icon in Studio Pro are published and intended for your use. Actions without the icon are internal implementation details and shouldn't be called directly from your microflows.

**Deprecations with clear replacements** — Several older microflows have been deprecated, but each has a direct replacement. We've documented these in the migration guide so you know exactly what to use instead.

**Login and Logout behaviour refined** — The `Login` Java action no longer returns a Boolean—it either succeeds or throws an exception, making error handling more predictable. The `Logout` action now returns a Boolean to indicate success. The older `ExecuteLogin` and `ExecuteLogout` microflows have been deprecated in favor of the direct `Login` and `Logout` microflows.

**FileType is now NamedReference** — For consistency with Teamcenter naming conventions, we've renamed `FileType` and `File Type` to `NamedReference` and `Named Reference` throughout entities and microflows. This is a breaking change—you'll need to update references in your existing microflows.

**CreateBOMWindow improvements** — The `CreateBOMWindow_Generic` microflow has been updated: its parameters are now optional, and we've split off a `ByConfigurationContext` variant for cases where you need that level of control. The pre-configured variants have been removed to simplify the API surface.

**Fix a bug where provision of a ConfigurationName to Java actions changes the behavior** — The `ModelObject` Java class now requires `ConfigurationName` to be set in all cases. Previously, the behavior varied depending on whether you passed a configuration name or not—this inconsistency has been resolved.

**Better error handling** — The Connector no longer shows in-app error messages automatically. Instead, all error conditions throw exceptions, giving your microflows full control over how to handle and present errors to users. This makes error handling more predictable and testable.

**SSO improvements** — We've enhanced error logging around SSO configuration to make troubleshooting easier. Teamcenter SSO is now compatible with Teamcenter 2406 (previously required 2512 or higher). User provisioning has also changed: the `TcSSOUserInformation` entity now only contains `User` and `Locale`—use the `User` attribute (not `sub`) to get the Teamcenter username.

**Attributes updated** — Some attribute lengths have been updated for BOMLine, ReviseItemRevision and User entities to match Teamcenter's default values, reducing the chance of data truncation issues.

**More flexible URL handling** — Teamcenter URLs without a port number, without a trailing slash, or without a top-level domain now work correctly. This makes configuration more forgiving.

#### Teamcenter Extension changes

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
