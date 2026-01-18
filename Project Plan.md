# Project Plan Parser
## 1. Project Overview

### 1.1 Purpose

<!-- What problem does this project solve? Why does it exist? -->
The project reduces the time and effort required to create project plans. Writing plans is often repetitive, error prone, and inconsistent across projects. This tool provides a structured template and a guided GUI that helps users create complete, well organized project plans with consistent formatting and coverage of key planning areas.
## 2. Scope & Constraints

### 2.1 In Scope
<!-- Features, systems, or responsibilities included -->
- GUI for creating and editing all sections of a project plan.
- Ability to define:
    - Project overview details
    - Scope boundaries
    - Stakeholders and users
    - Assumptions and risks
    - High-level requirements
    - Technical stack
    - Database schemas
    - Epics with subtasks and acceptance criteria
- Export of the project plan as Markdown.
### 2.2 Out of Scope
<!-- Explicit exclusions to prevent scope creep -->
- Multi-user collaboration
- Project execution features (timelines or task assignments)
## 3. Stakeholders & Users

### 3.1 Target Users
<!-- Who will use this? -->
Software engineers and technical leads familiar with Markdown and project planning concepts. Users should understand the projects they are documenting.
### 3.2 Stakeholders
<!-- Decision makers, reviewers, or affected teams -->
- Project owners
- Future and current contributors or maintainers of a project
## 4. Assumptions & Risks

### 4.1 Assumptions
<!-- Things assumed to be true but not guaranteed -->
- Users understand their project well enough to provide the necessary content
- User is familiar with markdown formatting

### 4.2 Risks
<!-- Known risks and mitigation strategies -->

| Risk                                                       | Impact | Likelihood | Mitigation                                                   |
| ---------------------------------------------------------- | ------ | ---------- | ------------------------------------------------------------ |
| Users provide incomplete or inaccurate project information | Medium | Medium     | Provide clear prompts, validation hints, and example content |
| GUI usability issues                                       | High   | Medium     | Conduct usability testing with target users before release   |
| Data loss during plan creation                             | High   | Low        | Autosave feature, versioning, and local backups              |
| Exported Markdown formatting issues                        | Medium | Low        | Validate Markdown rendering during export, provide preview   |

## 5. High-Level Requirements

### 5.1 Functional Requirements

<!-- What the system must do (high level) -->
#### 1. Project Overview
- A textbox will be given where the user is prompted to write a 3-5 sentences of the purpose of the project.
- The follow text will be written as prompt text in the textbox: "What problem does this project solve? Why does it exist?"

#### 2. Scope & Constraints
- Text lists for “In Scope” and “Out of Scope” items.
- Support nested child items.
- Users can add, delete, or reorder items.
#### 3. Stakeholders & Users
Same structure as Scope lists, for Target Users and Stakeholders.
#### 4. Assumptions & Risks
- Lists for Assumptions and Risks.
- Support nested child items, addition, deletion, and reordering.
#### 5. High-Level Requirements
- Each section stated within this project plan will have its own text box (ex, 1. Project Overview, 2. Scope & Constraints, 3. Stakeholders & Users) listing its functional requirements, nonfunctional requirements
#### 6. Technical Stack
- Users can specify frontend and backend tools.
#### 7. Database Schema

##### Database Type

- Users can select the database modeling type from a dropdown:
    - JSON
    - SQL
    - MongoDB
    - TypeScript    
- Changing the database type will reset any existing models to prevent incompatibility.
    
##### Models
- Each model must have a unique **name**.
- Users can **add, delete, and reorder models** within the GUI.
    - Deleting a model will also remove all its properties.
- Within a model, users can **add properties**, each requiring:
    - **Property Name**
    - **Property Type**
- As the model is updated, an **immutable code block preview** shows how the model will appear in the project plan.
- Users can **reorder properties** within a model to control their display order in the project plan.
#### 8. Epics
- Users should be able to write the following for each epic
	- Epic name
	- Sub task information in a table
		- Name of the subtask
		- Description of the sub task
		- A user story for the subtask
	- A checklist of acceptance criteria for each sub task
- Users are allowed to move the order of how epics are written, and the project will automatically update the numbers accordingly
- User can rearrange the order for  sub story and the acceptance criteria will follow
- User can delete
	- An entire epic
	- A specific sub story. This will delete the corresponding acceptance criteria, and via versa
### 5.2 Non-Functional Requirements
<!-- Performance, reliability, security, usability, etc. -->
- **Performance:** The GUI should load and respond within 200ms for all operations with ≤100 plan items.
- **Reliability:** Autosave functionality to prevent data loss.
- **Usability:** Intuitive interface with clear prompts and nested list management.
- **Security:** Local file storage only; no external data transmission in v1.
## 6. Technical Stack

### Frontend
<!-- What tools are required for the frontend to work  -->
- TypeScript
- Electron (desktop GUI framework)
### Backend
<!-- What tools are required for the backend to work  -->
- Local storage using JSON files
- Autosave and versioning support
## 7. Database Schema

### NestedItem
Represents a list of an item that has the option to be nested
#### TS Layout
```ts
interface NestedItem {
  text: string;               // The content of this scope item
  children?: NestedItem[];    // Optional nested items
}
```
#### JSON Example
```json
[
  {
    "text": "Implement core GUI",
    "children": [
      {
        "text": "Create Project Overview editor"
      },
      {
        "text": "Create Scope editor",
        "children": [
          {
            "text": "Support nested scope items"
          },
          {
            "text": "Add, delete, reorder items"
          }
        ]
      }
    ]
  },
  {
    "text": "Add Markdown export functionality"
  }
]

```

### ProjectPlan
Represents a project plan
#### TS Layout

```ts
interface ProjectPlan {
  _id: ObjectId;
  title: string;
  purpose: string;
  scope_in: NestedItem[];      // Nested in-scope items
  scope_out: NestedItem[];     // Nested out-of-scope items
  target_users: NestedItem[];  // Nested optional if you want sub-users
  stakeholders: NestedItem[];  // Nested optional
  assumptions: NestedItem[];   // Nested optional
  risks: NestedItem[];         // Nested optional
  requirements: {
    functional: NestedItem[];
    non_functional: NestedItem[];
  };
  technical_stack: {
    frontend: string[];
    backend: string[];
  };
  database_type: "JSON" | "SQL" | "MongoDB" | "TypeScript";
  models: Model[];
  epics: Epic[];
}
```

#### JSON Example
```json
{
  "_id": "ObjectId",
  "title": "New Project Plan",
  "purpose": "Create a tool to streamline project plan creation.",
  "scope_in": [
    {
      "text": "GUI for all plan sections",
      "children": [
        { "text": "Project Overview editor" },
        { "text": "Scope editor" },
        { "text": "Stakeholders & Users editor" }
      ]
    }
  ],
  "scope_out": [
    { "text": "Multi-user collaboration" },
    { "text": "Timeline management" }
  ],
  "target_users": [
    { "text": "Software Engineers" },
    { "text": "Technical Leads" }
  ],
  "stakeholders": [
    { "text": "Project Owners" },
    { "text": "Maintainers" }
  ],
  "assumptions": [
    { "text": "User knows their project well" },
    { "text": "User is familiar with Markdown" }
  ],
  "risks": [
    {
      "text": "Incomplete or inaccurate information",
      "children": [
        { "text": "Provide prompts and validation hints" }
      ]
    }
  ],
  "requirements": {
    "functional": [
      { "text": "Create and edit all project plan sections" },
      { "text": "Export to Markdown" }
    ],
    "non_functional": [
      { "text": "GUI responds within 200ms for ≤100 items" },
      { "text": "Autosave to prevent data loss" }
    ]
  },
  "technical_stack": {
    "frontend": ["TypeScript", "Electron"],
    "backend": ["JSON file storage", "Autosave"]
  },
  "database_type": "JSON",
  "models": [
    {
      "name": "Account",
      "properties": [
        { "name": "_id", "type": "ObjectId" },
        { "name": "name", "type": "String" }
      ]
    }
  ],
  "epics": [
    {
      "name": "Set Up Database",
      "subtasks": [
        {
          "name": "Provision MongoDB",
          "description": "Start a local Docker container or Atlas cluster; create app/admin user.",
          "user_story": "As a developer, I want a database available so I can persist data.",
          "acceptance_criteria": [
            "MongoDB is deployed (Docker or Atlas).",
            "Application user created with least-privilege role."
          ]
        }
      ]
    }
  ]
}

```
## 8. Epics
### Epic 1: Set Up Database Schema

| **Name**                           | **Description**                                           | **User Story**                                                            |
| ---------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------- |
| **1.1 - Define ProjectPlan Model** | Create TypeScript interface representing the ProjectPlan. | As a developer, I want a type-safe model to store project plans.          |
| **1.2 - Implement Model Storage**  | Implement local JSON file storage for ProjectPlan.        | As a developer, I want to save project plans persistently.                |
| **1.3 - Test Model Read/Write**    | Write scripts to insert, update, and retrieve plans.      | As a developer, I want to verify that saving and loading works correctly. |
##### 1.1 - Define ProjectPlan Model Acceptance Criteria
- [ ]  TypeScript interfaces created for ProjectPlan, Model, and Epic.
- [ ]  All required fields (title, purpose, scope, stakeholders, epics) included.
- [ ]  Interfaces support nested lists (scope, requirements, epics).
##### 1.2 - Implement Model Storage Acceptance Criteria
- [ ]   JSON files are created for each project plan.
- [ ]   CRUD operations (create, read, update, delete) work for models and epics.
- [ ]   Model property changes are reflected in stored JSON.
##### 1.3 - Test Model Read/Write Acceptance Criteria
- [ ]   A sample project plan is saved successfully.
- [ ]   Saved plan can be loaded and matches the original data.
- [ ]   Nested epics and subtasks are correctly stored and retrieved.


#### Epic 2: Project Overview Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**2.1 - Project Overview Textbox**|Add textbox for project purpose and overview details.|As a user, I want to write a clear project purpose in the GUI so I can communicate the project goal effectively.|

##### 2.1 Acceptance Criteria
- [ ]  Textbox allows multiple sentence input.
    
- [ ]  Prompt text is visible: “What problem does this project solve? Why does it exist?”
    
- [ ] Input supports basic Markdown formatting.
    
- [ ]  Changes are immediately saved to the ProjectPlan model.
    
- [ ]  Validation prevents empty submissions or text exceeding 500 characters.
    

---

#### Epic 3: Scope & Constraints Editor

| **Name**              | **Description**                                                              | **User Story**                                                                                       |
| --------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **3.1 - Scope Lists** | Add interface to manage in-scope and out-of-scope lists with nested support. | As a user, I want to define scope items with nested entries to clearly represent project boundaries. |

##### 3.1 Acceptance Criteria

- [ ]  Users can do the following with scope items
	- [ ] add
	- [ ] delete
	- [ ] reorder
- [ ]  Nested items can be created to any depth.
- [ ]  Drag-and-drop support for reordering items and sub-items.
- [ ] Validation prevents duplicate top-level items.
- [ ]  Changes are persisted in the ProjectPlan model.
- [ ]  Visual indicators distinguish in-scope vs out-of-scope items.
---
#### Epic 4: Stakeholders & Users Editor

| **Name**                              | **Description**                                        | **User Story**                                                                        |
| ------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------- |
| **4.1 - Stakeholders & Target Users** | Add interface to define target users and stakeholders. | As a user, I want to list all stakeholders in the plan so responsibilities are clear. |
##### 4.1 Acceptance Criteria
- [ ]  Users can add/remove stakeholders and target users.
- [ ]  Fields include name, role, and contact information (optional).
    
- [ ]  Reordering stakeholders is possible via drag-and-drop.
    
- [ ]  Validation prevents duplicate names within the same section.
    
- [ ]  Changes are saved immediately to the ProjectPlan model.
    

---

#### Epic 5: Assumptions & Risks Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**5.1 - Assumptions & Risks Lists**|Add list interface for assumptions and risks.|As a user, I want to clearly define assumptions and potential risks to mitigate project issues.|

##### 5.1 Acceptance Criteria

- [ ]  Users can add/delete/reorder assumptions and risks.
    
- [ ]  Each risk can include optional fields: impact, likelihood, and mitigation notes.
    
- [ ]  Nested items for assumptions or risks are supported.
    
- [ ]  Validation warns if required risk fields (impact, likelihood) are missing.
    
- [ ]  Changes are saved in the ProjectPlan model.
    

---

#### Epic 6: High-Level Requirements Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**6.1 - Functional & Non-Functional Requirements**|Add interface to define requirements per section.|As a user, I want to specify functional and non-functional requirements to clearly define project expectations.|

##### 6.1 Acceptance Criteria

- [ ] Users can add/delete/reorder functional requirements.
    
- [ ]  Users can add/delete/reorder non-functional requirements.
    
- [ ]  Requirements are associated with the correct section (Project Overview, Scope, etc.).
    
- [ ] Validation ensures no empty requirements.
    

---

#### Epic 7: Technical Stack Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**7.1 - Frontend & Backend Tools**|Add interface to specify frontend and backend tools.|As a user, I want to define the technical stack for the project so future contributors know the environment.|

##### 7.1 Acceptance Criteria

- [ ]  Users can add/remove frontend tools.
    
- [ ]  Users can add/remove backend tools.
    
- [ ]  Validation prevents duplicate entries.
    
- [ ]  Changes persist to the ProjectPlan model.
    

---

#### Epic 8: Database Schema Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**8.1 - Database Type & Models GUI**|Add editor for database type, models, and properties.|As a user, I want to define the schema visually in the GUI so I can clearly communicate the data model.|

##### 8.1 Acceptance Criteria

- [ ]  Users can select database type (JSON, SQL, MongoDB, TypeScript).
    
- [ ]  Adding a model requires a unique name.
    
- [ ]  Users can add/delete/reorder models.
    
- [ ]  Within a model, users can add/delete/reorder properties.
    
- [ ]  Property requires name and type.
    
- [ ]  Immutable code preview updates automatically with changes.
    
- [ ]  Validation prevents duplicate property names within a model.
    

---

#### Epic 9: Epics Editor

|**Name**|**Description**|**User Story**|
|---|---|---|
|**9.1 - Epics & Subtasks**|Add table interface for epics, subtasks, and acceptance criteria.|As a user, I want to define epics with structured subtasks and checklists to organize work clearly.|

##### 9.1 Acceptance Criteria

- [ ]  Users can add/delete/reorder epics.
    
- [ ]  Users can add/delete/reorder subtasks.
    
- [ ]  Acceptance criteria checklists update automatically when subtasks change.
    
- [ ]  Reordering subtasks preserves associated checklists.
    
- [ ]  Validation prevents empty epic or subtask names.
    
- [ ]  Changes persist to the ProjectPlan model.
