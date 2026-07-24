## 2606 {#2606}

### 2606.0.0 {#26060}

**Release date: TBD**

{{% button color="info" href="https://marketplace.mendix.com/link/component/" text="Go to Marketplace" title="Download version 2606.0.0 from the Marketplace" %}}

#### What's New in 2606?

Version 2606 of the Teamcenter Connector significantly consolidates and modernizes Teamcenter integrations. The Teamcenter Connector now includes a new Studio Pro extension for configuring your integrations, a complete rebuild with many quality of life improvements. We consolidated everything into one place. Previously, you had to download the Teamcenter Connector module and the Teamcenter Extension (which contained Teamcenter Toolkit and the Extension as part of an add-on module) separately. Now it is one single download, one import, and one version to track. The Teamcenter Toolkit has been integrated into the TcConnector module, with all of its microflows, entities, and Java actions.

Version 2606 requires Mendix Studio Pro 11.12.1 or higher.

#### New Features

##### Teamcenter Service Document

We introduced the Teamcenter Service document, a new design-time approach that changes how you build Teamcenter integrations. The service document is your starting point and central hub for managing all integrations within a module. It replaces the previous menu-based Extension that stored configuration files on disk. The document contains two main sections:

- **Settings** tab where you configure your Teamcenter connection and authentication (with automatic credential clearing when connection details change for security).
- **Integrations** tab that provides an overview of all configured integrations, showing generated entities and microflows. From the Integrations tab, you can view, edit, duplicate, or delete integrations. You can create multiple service documents within your application, each in a separate module. This enables you to organize integrations by concern (for example, separate documents for parts, workflows, and documents).

To create a service document, right-click a module in the App Explorer and select **Add other** > **Teamcenter service**. Once configured, the Extension generates everything you need: entities, associations, create/revise input entities, and microflows. This keeps your integration artifacts organized and version-controlled alongside your other Mendix documents.

##### Mendix in AWC

We made some changes to the Teamcenter connector that are required for embedding Mendix applications in Teamcenter Active Workspace Client. For more information, see [Mendix inside Teamcenter](/refguide/mendix-client/mendix-inside-teamcenter/). These changes also give you more control over session management and authentication when building Mendix applications that integrate with Teamcenter:

- We added an optional session discriminator on Login and LoginSSO. Credentials now has a new discriminator field; empty means unchanged behavior. This allows an embedded Mendix component to maintain separate sessions when needed.
- We added a session discriminator on the /tcsso/login service, so an embedded Mendix component can now reuse the active AWC Teamcenter session.
- We introduced a new anonymous REST endpoint to check whether the current session is logged in to Teamcenter without requiring authentication.

#### Improvements

##### TcConnector

- We hardened security by removing Create and Delete rights from all entities to align with security best practices. The Administrator role no longer has direct entity-level access (except for Teamcenter Configuration). For all Teamcenter integrations, you should now use the User role.
- We improved SSO configuration error logging. Messages now name the fields that may be wrong and match the Teamcenter Extension.
- We added a consistent Teamcenter icon on all published actions. Actions marked with a Teamcenter icon in Studio Pro are published and intended for your use. Actions without the icon are internal implementation details and shouldn't be called directly from your microflows.
- We upgraded to Mendix 11.12.1.
- We established clearer public and private boundaries in the structure of the TcConnector module. The Teamcenter Connector now has a well-defined public API.

##### Teamcenter Extension

- We rebuilt the Teamcenter Extension using the Mendix Web Extensibility Framework (TypeScript). Previously, it used the C# Extensibility Framework, which Mendix is discontinuing. This ensures continued compatibility with future Mendix releases.
- We redesigned the Teamcenter Extension to follow the Studio Pro Design System. As a result, the extension looks and feels like a native part of Studio Pro even more so than the C# extension. The experience is more consistent with the rest of your development environment.
- We introduced the Teamcenter Service Document. This document stores your integrations in the Mendix model. You can create multiple Service Documents per app/module. This enables you to organize your Teamcenter integrations by functional area (for example, a Parts module and a Workflows module can each have their own Service Document) or by Teamcenter environment. An added benefit of the Teamcenter Service Document is that integrations and Teamcenter instance settings are now stored as a document within your module, just like microflows and pages. The Teamcenter Service Document is the new entry point for your Teamcenter integrations and replaces the previous menu-based extension that stored configuration files on disk.
- We introduced Teamcenter Service Document consistency checks. The extension validates your integrations and shows consistency checks when your integration gets corrupted. The extension listens to model changes to make sure the consistency checks remain in sync. The consistency checks are displayed as warnings in the Error List. This also allowed us to clean up the UI of the integrations overview. Instead of showing warning in the extension, we now refer to the Error List, if there are consistency errors.
- We removed the sidebar from the import mapping page. Instead, the extension uses dialogs for selecting properties and configuring integration specific properties. This is more in line with Mendix Studio Pro.
- We added a button to the toolbar of the import mapping page to launch the property mapping dialog for the selected business object / entity.
- We introduced constants to store the secrets used in the TeamcenterX SSO configuration for the Teamcenter extension. This way, you can choose to keep them secret and prevent committing them to version control.
- We added the option to clear the object mapping for a business object / entity by selecting either and pressing Delete
- We added a new annotation to the Get structure integration that displays the integration specific properties: which revision rule to use, if variant rules are used and if BOM window properties are configured at design time.

#### Fixes

- We improved the error message when the provided configuration name does not exist.
- We fixed a blocking (locked) state when calling multiple Teamcenter Java actions within one transaction. This issue could cause one locked user to prevent other users from executing Teamcenter operations.
- We fixed an error when a Teamcenter response contained an array mixing empty strings and valid Model Object UIDs. (Ticket 269666)
- We updated attributes on BOMLine, ReviseItemRevision, User and WorkspaceObject entities to match Teamcenter defaults, reducing the chance of data truncation:
  - BOMLine bl_rev_object_name (128)
  - BOMLine bl_quantity (24)
  - BOMLine bl_plmxml_abs_xform (240)
  - ReviseItemRevision.item_revision_id (32)
  - User.userid (32)
  - WorkspaceObject.checked_out (32)
- WhereUsed2 now correctly maps clientId and level in its response.
- We fixed a misleading error "Entity does not exist: ListOfValues" from GetAttachedLOV and GetAttachedPropDescs2.
- We renamed FileType/File Type to NamedReference/Named Reference for consistency with Teamcenter naming conventions.

#### Deprecations

We deprecated the following elements from the Teamcenter Connector. Deprecated elements are excluded and will be removed in a future release but continue to ship until then. Replacing them is recommended.

##### Deprecated microflows with a replacement:

- DownloadFile — use the DownloadFile/DownloadImage Java actions instead
- ExecuteLogin — use the Login microflow instead
- ExecuteLogout — use the Logout microflow instead

##### Deprecated microflows without a direct replacement:

See the annotation on each microflow for guidance:

- AreMultipleTcConfigActive
- CloseBOMWindow
- HandleActiveConfigErrors
- HandleServiceErrors
- RetrieveConfigNameFromSingleActiveConfiguration
- RetrieveHttpHeaderList
- RetrieveTcSessionBasedOnConfigName
- RetrieveTeamcenterConfigurationByName
- RetrieveTeamcenterConfigurationFromTcSession
- ShowPartialErrors
- UpdateSession

##### Deprecated Java actions:

See each action's documentation for its successor:

- CreateBOMWindows
- CreateBOMWindows2
- ExpandPSOneLevel
- GetItemFromId
- GetTcSessionInfo
- GetWorkflowTemplates
- PerformAction
- RetrieveCookie
- WhereUsed

##### Deprecated entities:

The following entities have been deprecated. To indicate this, the entities have been renamed with an underscore prefix:

- CreateBomWindowInput
- CreateBomWindowInput_CreateBomWindowResponse
- CreateBomWindowResponse
- ExpandPSOneLevelResponse
- GetItemFromIdInput
- GetItemFromIdResponse
- GetWorkflowTemplatesInput
- ItemRevisionOutput
- RevisionIDs
- SessionUser
- TcServerInfo
- WhereUsedInput
- WhereUsedResponseInfo

##### Moved to Private

No longer published in the toolbox - use the wrapper microflow of the same name instead:

- Login Java action — use the Login microflow
- Logout Java action — use the Logout microflow
- LoginSSO Java action — set up the SSO login flow as described in the documentation

#### Breaking Changes

##### Resources folder

- We changed the structure of the resources folder. All resources for the connector are now in the `Resources/TcConnector` folder.
- When upgrading, please move all custom Operation Mapping JSONs to `Resources/TcConnector/OperationMapping` and remove the old Operation Mapping folder (`Resources/OperationMapping`).
- The folder `Resources/TeamcenterCommon` may be removed.
- The folder `Resources/TeamcenterConnector` may be removed.
 
##### Teamcenter Toolkit Integration

- We integrated the Teamcenter Toolkit content into the Teamcenter Connector. Its microflows, folders and entities are now in the Connector under the same names; BOMLine entities were merged and the search-criteria entities moved. Regenerate your integration with the Teamcenter Extension to resolve references to the microflows and entities.

##### Removed Actions

We removed the following Microflow and Java Actions. If they are still used in your project, please retain the old TeamcenterToolkit module until you have migrated away from these actions:

- ModelObject_AddToExistingOrNewList
- JA_CopyModelObjectToNewCreateInput
- JA_CreateDatasetSpecialization
- The pre-configured CreateBOMWindow variants (CreateBOMWindow_Default, CreateBOMWindow_RevisionRule, CreateBOMWindow_VariantRule and their combinations) — use CreateBOMWindow or CreateBOMWindow_ByConfigurationContext instead. We updated the CreateBOMWindow microflow: its parameters are now optional, and we've split off a separate ByConfigurationContext variant for cases where you need that level of control.

##### Security Model

- We removed Create and Delete rights on all entities. The Administrator role now only has access to Teamcenter Configuration. Use the User module role for Teamcenter integrations.

##### Error Handling

- We changed the error handling behavior. The Connector no longer shows in-app error messages automatically. Instead, all error conditions throw exceptions, giving your microflows full control over how to handle and present errors to users.

##### Configuration Requirements

- We fixed a bug where provision of a ConfigurationName to Java actions changes the behavior. The `ModelObject` Java class now consistently requires `ConfigurationName` to be set in all cases. Previously, the behavior varied depending on whether you passed a configuration name or not.

##### Entity Changes

- We removed the LastError attribute from the Credentials entity. Use exception handling instead.
- We introduced a new naming convention for CreateInput and CompoundCreateInput specializations generated by the Teamcenter Service document. If you have existing integrations, you'll need to regenerate them by creating a new Teamcenter Service document, configure your integration, and generate the artifacts. Note that regeneration will not delete your previous input entities, so these need to be removed manually.
