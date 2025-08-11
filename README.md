# ServiceNow Client Scripts Project

## Project Overview
This project demonstrates the creation and execution of ServiceNow **Client Scripts** to enforce specific behaviors and restrictions in the **Incident** table.  
It covers:

# ServiceNow Incident Management - Client Script Flow

ServiceNow Incident Management
   │
   ├─ onLoad() Client Script
   │      → Form Load Event
   │      → Display Alert: "Form Ready"
   │
   └─ onCellEdit() Client Script
          → List View Inline Edit
          → New State Value?
               ├─ Resolved (6) → Block Change + Show Alert
               ├─ Closed (7)   → Block Change + Show Alert
               └─ Other        → Allow Save


1. Creating an **onLoad() Client Script** to display an alert when an Incident form is fully loaded.
2. Creating an **onCellEdit() Client Script** to prevent setting the **State** of an Incident to `Resolved` or `Closed` directly from a **List View**.

---


ServiceNow Incident Management
   ↓
onLoad() Client Script → Form Load Event → Display Alert: "Form Ready"

ServiceNow Incident Management
   ↓
onCellEdit() Client Script → List View Inline Edit → New State Value?
      ├─ Resolved (6) → Block Change + Show Alert
      ├─ Closed (7)   → Block Change + Show Alert
      └─ Other        → Allow Save


##  Case Study Cover Diagram
```mermaid
flowchart TD
    A[ServiceNow Incident Management] --> B[onLoad() Client Script]
    A --> C[onCellEdit() Client Script]

    B --> D[Form Load Event]
    D --> E[Display Alert: "Form Ready"]

    C --> F[List View Inline Edit]
    F --> G{New State Value?}
    G -->|Resolved (6)| H[Block Change + Show Alert]
    G -->|Closed (7)| I[Block Change + Show Alert]
    G -->|Other| J[Allow Save]



## SMART Objectives
### Specific:
### Implement ServiceNow Client Scripts to:

Display a form load confirmation message.

Prevent unauthorized inline state changes in Incident records.

Measurable:
Ensure that:

100% of form loads display the alert.

100% of inline edits to Resolved or Closed are blocked.

Achievable:
Uses only native ServiceNow Client Script functionality without third-party integrations.

Relevant:
Improves user guidance and maintains Incident process compliance.

Time-bound:
Developed and tested in the ServiceNow training instance.




## Key Definitions

### **Client Script**
A piece of JavaScript executed on the **client-side** (browser) in ServiceNow.  
It allows you to manage form behavior, validate data, and perform actions without a full server round trip.

---

### **UI Type**
Specifies where the script will run:
- **Desktop** – Script runs only in the standard web UI.
- **Mobile** – Script runs in the mobile app UI.

---

### **onLoad Client Script**
Triggered **when a form is loaded**.  
Used to set default values, control field visibility, or display messages for users.

---

### **onCellEdit Client Script**
Triggered **when a single field is edited in a list view** and saved without opening the full record form.  
Useful for restricting inline edits or validating changes at the list level.

---

## Step-by-Step Process

---

### **1. Generate onCellEdit Alert (Screenshot 1)**

**Purpose:**  
To stop users from changing the **State** field to `Resolved` or `Closed` directly from the Incident List View.

**Configuration:**
- **Table:** `Incident [incident]`
- **UI Type:** Desktop
- **Type:** onCellEdit
- **Field name:** State
- **Description:** Prevents list view updates to restricted states.

**Script Logic:**
```javascript
function onCellEdit(sysIDs, table, oldValues, newValue, callback) {
    var saveAndClose = true;

    // Restrict setting State to 'Resolved' from the list
    if (newValue == 6) { // Resolved
        alert("You cannot change the state to 'Resolved' from the list");
        saveAndClose = false;
    }

    // Restrict setting State to 'Closed' from the list
    if (newValue == 7) { // Closed
        alert("You cannot change the state to 'Close' from the list");
        saveAndClose = false;
    }

    callback(saveAndClose);
}


## What Happens:

When a user edits the State column in the list:

If new value is Resolved (6) or Closed (7), an alert appears.

Save action is prevented for the edit.


2. Generate onLoad Alert (Screenshot 2)
Purpose:
To notify users when the Incident form has fully loaded.

### Configuration:

Table: Incident [incident]

UI Type: Desktop

Type: onLoad

Description: Generates an alert when form loads.

Script Logic:

function onLoad() {
    alert("The form has finished loading and is ready for user input.");
}


### What Happens:

When the Incident form opens:

An alert is displayed to the user confirming the form is ready.

3. Incident List View with onCellEdit in Action (Screenshot 3)
Purpose:
To demonstrate the onCellEdit restriction in a real environment.

Steps:

Navigate to Incident List View.

Attempt to change the State column directly to Resolved or Closed.

The onCellEdit Client Script triggers an alert and prevents the update.

Outcome:

Inline edit is blocked for restricted states.

Encourages users to open the full record for such changes.


3. Incident List View with onCellEdit in Action (Screenshot 3)
Purpose:
To demonstrate the onCellEdit restriction in a real environment.

Steps:

Navigate to Incident List View.

Attempt to change the State column directly to Resolved or Closed.

The onCellEdit Client Script triggers an alert and prevents the update.

Outcome:

Inline edit is blocked for restricted states.

Encourages users to open the full record for such changes.



Flow Diagrams
onLoad() Flow

flowchart TD
    A[User opens Incident Form] --> B[ServiceNow loads form]
    B --> C[onLoad() Client Script runs]
    C --> D[Display alert: "The form has finished loading"]
    D --> E[Form ready for user input]


onCellEdit() Flow

flowchart TD
    A[User edits State field in List View] --> B[Check new value]
    B -->|Resolved (6)| C[Show alert: Cannot change to Resolved]
    B -->|Closed (7)| D[Show alert: Cannot change to Closed]
    B -->|Other values| E[Allow save]
    C --> F[Cancel save]
    D --> F
    E --> G[Save change]


Sequence Diagrams
onLoad() Sequence

sequenceDiagram
    participant User
    participant Browser
    participant ServiceNow

    User->>ServiceNow: Open Incident Form
    ServiceNow-->>Browser: Load form data
    Browser->>Browser: Execute onLoad() script
    Browser-->>User: Show alert "Form has finished loading"


onCellEdit() Sequence


sequenceDiagram
    participant User
    participant Browser
    participant ServiceNow

    User->>Browser: Edit State in List View
    Browser->>Browser: Execute onCellEdit() script
    alt newValue == Resolved or Closed
        Browser-->>User: Show alert (blocked change)
        Browser->>ServiceNow: Cancel save
    else
        Browser->>ServiceNow: Save change
        ServiceNow-->>Browser: Confirm save
    end



Testing
Testing onLoad() Script
Test Case 1: Form Load Alert

Action: Open any Incident record in form view.

Expected Result: An alert box appears saying:


The form has finished loading and is ready for user input.

Status: ✅ Passed.


Testing onCellEdit() Script
Test Case 2: Prevent State Change to Resolved

Action: In Incident List View, inline-edit the State column to Resolved.

Expected Result: Alert appears:



You cannot change the state to 'Resolved' from the list

Change is not saved.

Status: ✅ Passed.

Test Case 3: Prevent State Change to Closed

Action: In Incident List View, inline-edit the State column to Closed.

Expected Result: Alert appears:



You cannot change the state to 'Close' from the list

Change is not saved.

Status: ✅ Passed.

Test Case 4: Allow Other State Changes

Action: In Incident List View, inline-edit the State column to any value other than Resolved or Closed.

Expected Result: Change is saved successfully without alerts.

Status: ✅ Passed.



Potential Improvements
Replace Alerts with UI Notifications

Instead of using alert(), use ServiceNow’s g_form.addInfoMessage() for a less intrusive user experience.

Role-Based Restrictions

Allow certain roles (e.g., admin, itil_admin) to bypass restrictions.

Multi-Language Support

Display messages in the user’s preferred language using ServiceNow’s gs.getMessage() function.

Logging

Log blocked changes for auditing purposes using server-side logging or business rules.

Dynamic Configuration

Store restricted state IDs in a system property so they can be updated without modifying the script.

Author: Gerald Chima Ukwuoma
Developed in a ServiceNow training lab environment to demonstrate client-side scripting skills.

