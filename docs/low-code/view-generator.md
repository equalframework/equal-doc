eQual leverages a **view generator** and **UI components framework** to create dynamic and structured user interfaces. The central part of the screen is managed by a **frame** that holds a **stack of contexts**, allowing for dynamic navigation and interaction with data.

When opening a view (e.g., by clicking on an item in a list), eQual creates a new context and pushes it onto the top of the stack. Switching back to a previous view pops the context off the stack. Apps define the initial bootstrap view, and menus structure a series of view descriptors with links to load corresponding contexts.




### 1. **Stacking of Contexts**

#### What is a Context?
A **context** defines the scope and environment in which a view operates. It includes:
- The **entity** being displayed or modified (e.g., `User`, `Post`).
- The **view type** (e.g., `list.default`, `form.default`).
- Additional parameters like domain (filter), actions, or associated routes.

#### How Context Stacking Works
Context stacking manages **nested or hierarchical contexts** for interacting with multiple data layers or UI components. Each new context is shown on the top of the previous one, creating a logical stack:
1. **Initial Context**: Application-specific context related to the initial view.

2. **Subcontexts**: Nested contexts for related entities or actions.

    

!!! Note "Contexts linked to routes (url)"
    Alternatively, some contexts can be associated to specific routes .



### 2. **Types of Views**

eQual supports various view types tailored to specific use cases:

#### a. **List Views (`list.default`)**
- **Purpose**: Display multiple objects in a grid or table format.
- **Example**: A user list showing `name`, `email`, and `role`.
- **Features**:
  - Pagination, sorting, and filtering.
  - Domain filters applied dynamically (e.g., `domain=[name,like,John]`).

#### b. **Form Views (`form.default`)**
- **Purpose**: Enable creation or modification of a single entity.
- **Example**: A form to update user details.
- **Features**:
  - Input widgets for each field.
  - Dynamic validation and behavior based on context.

#### c. **Chart Views (`chart.default`)**
- **Purpose**: Visualize data in charts or graphs.
- **Example**: A pie chart showing user roles distribution.
- **Features**:
  - Integrates with computed or aggregated data fields.

#### d. **Dashboard Views**
- **Purpose**: Summarize data from multiple views or entities.
- **Example**: A dashboard showing user statistics and recent activity.
- **Features**:
  - Aggregates multiple subcontexts and views.

#### e. **Custom Views**
- **Purpose**: Provide flexibility for unique requirements.
- **Example**: A calendar view for scheduling events.
- **Features**:
  - Custom layouts and behaviors defined programmatically or in JSON.

---

### 3. **Interaction Between Contexts and Views**

When users interact with the application:
1. **Context Stack Evolves**: Each interaction (e.g., opening a form) creates a new context atop the stack.
2. **Dynamic Data Scope**: The current context defines which data is fetched or filtered.
3. **Rendering Views**: UI components are rendered based on the context, including inherited and user-applied filters.

For example:
- In a **list view**, clicking an item dynamically loads a **form view** with the selected entity’s details, while retaining the original context for navigation.
