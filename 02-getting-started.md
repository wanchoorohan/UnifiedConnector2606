## Getting Started

### Installation and Setup

**Download and import the module:**
1. Open Mendix Studio Pro 11.12, and go to Marketplace tab. Download Teamcenter Connector 2606. Remember, now you don't need to download Teamcenter Extension since its all in one module.
2. Alternatively, you can download Teamcenter Connector 2606 from marketplace and import it into your Mendix 11.12 project.

**Add required dependencies:**
3. Make sure your app includes Community Commons and the Encryption module. These are both available from the Marketplace.

**Configure security:**
4. Assign the `TcConnector.User` role to the relevant user roles in your app's security settings. **Important**: The Administrator role now only has access to Teamcenter Configuration. For all integration work, use the User role.
5. Click "Update Security" in Studio Pro to refresh entity access rules.

**Enable React client:**
6. Make sure the React client is enabled in your app settings (Project → Settings → Runtime). This is required for the Web Extension UI to work properly.


### Creating your First Teamcenter service document

**What a Service Document contains:**

1. **Settings** — How to reach Teamcenter and how to authenticate
  - This is where you configure the connection to Teamcenter
  - Set up your authentication method, test the connection, and manage sign-in state
  - You typically configure this once per Service Document, unless you need to switch environments

<img width="2339" height="1168" alt="image" src="https://github.com/user-attachments/assets/cfeb4f6a-83cf-4524-a2da-462447144f7d" />

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

<img width="3544" height="1700" alt="image" src="https://github.com/user-attachments/assets/33937f02-6003-4016-be11-95f18d03f62e" />

**You can have multiple Service Documents:**
You can create several Service Documents in a single app, each in its own module. This is useful for separating concerns. For example:
- A "Parts" module with a Service Document for part-related integrations
- A "Workflows" module with a Service Document for workflow-related integrations
- A "Documents" module with a Service Document for dataset and document integrations


**Create a Teamcenter Service Document:**
1. In the App Explorer, right-click on the module where you want to create your integrations
2. Select "Add document" → "Teamcenter Service Document"
3. Give it a name (e.g., "PartsIntegrations" or "MainTeamcenterConnection")

**Open the Settings tab:**
4. Double-click the Service Document you just created
5. Navigate to the Settings tab (it's one of the tabs at the top of the document)

**Configure your connection:**
6. In the Settings tab, provide your Teamcenter authentication option along with credentials. Please refer to the [old Teamcenter Extension](https://docs.mendix.com/appstore/modules/siemens-plm/teamcenter-extension/create-an-integration/#settings-tab) settings page for more details.

**Test your connection:**
7. Once you've entered your connection details, click the "Sign In" button to test the connection

**Important note about auto-logout:**
The Extension automatically clears your sign-in state when you edit or reset connection details. This is a security feature to ensure you're always using the correct credentials with the correct Teamcenter environment.

**Start your first journey**
8. Click on the "Integrations" tab at the top. Right now, it's empty—this is normal for a new Service Document
9. Click on "Add Integration"
10. You'll see tiles representing the 11 available integration types (like "Search Item Revisions," "Create Item with Item Revision," etc.)
11. Click on one of these tiles to start configuring that type of integration
12. This will take you to the Import Mapping page

**Step 4: Use the Import Mapping Mapping Page**
13. Double click on any one of the boxes to open the object mapping panel

**Teamcenter side (left):**
- Browse the Teamcenter object hierarchy
- Select the business object type you want to work with 

**Mendix side (right):**
- Choose whether to create a new entity or use an existing one
- If creating new, the Extension will generate a specialized entity for you
- If using existing, you can select an entity you've already defined in your domain model

**Property mapping (center):**
- Once you've selected the Teamcenter object type and Mendix entity, you can add additional properties to the list of default selected properties.
- Double click on the entity or click Select Elements on the top
- Use the checkboxes to indicate:
  - "Read" — should this property be retrieved from Teamcenter?
  - "Write" — should this property be sent to Teamcenter when creating or updating
- Select the properties you need for your integration

<img width="2551" height="1975" alt="image" src="https://github.com/user-attachments/assets/a137c186-da2c-4c8e-bd59-d6a2a4eea222" />

**Journey-specific panels:**
- Depending on the journey type, you'll see additional configuration panels
- For example:
  - Search journeys let you configure search criteria
  - Dataset journeys let you filter by dataset type
  - BOM journeys let you configure BOM window properties and revision rules
- Fill in these journey-specific settings as needed
- For more information on each of the journeys, refer to [integration guides]{https://docs.mendix.com/appstore/modules/siemens-plm/teamcenter-extension/create-an-integration/integrations/)

**Step 6: Generate your integration**
14. Once you've configured everything, click the "Generate" button
15. The Extension will:
    - Validate your configuration to make sure everything is correct
    - Generate domain model entities (placed in your domain model as a tree for easy visualization)
    - Generate microflows that implement the integration logic
    - Save the integration configuration to the History tab

**Step 7: Explore the results**
16. After generation completes, you'll automatically return to the Integrations tab
17. Find your newly created integration in the list
18. You'll see links to:
    - The generated microflows (click to jump directly to them in Studio Pro)
    - The generated entities (click to jump directly to the domain model)
19. Try clicking on a microflow name—Studio Pro will open that microflow, and you can see exactly what was generated
