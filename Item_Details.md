# Item Details Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor V as Visitor (Guest)
    participant UI as ItemDetailsPage (View)
    participant C as ItemController
    participant T as TimeServer (System)
    participant DB as ItemDatabase

    Note over V, UI: 1. User clicks on a specific item

    V->>UI: Click on Item (ID: 550)
    UI->>C: requestItemDetails(ID: 550)

    Note over C, DB: 2. Fetch Item Data

    C->>DB: getItemInfo(ID)
    activate DB
    DB-->>C: Return Description, Price, Images, EndTime
    deactivate DB

    Note over C, T: 3. Include: Get Server Time
    Note right of C: Critical for Auctions: Sync countdown<br/>with server to prevent lag.

    C->>T: getCurrentServerTime()
    activate T
    T-->>C: Return Precise Time (e.g., 12:00:01)
    deactivate T

    Note over C, UI: 4. Calculation & Display

    C->>C: Calculate Remaining Duration
    C-->>UI: Return Item Data + Server Time
    
    Note right of UI: UI starts the countdown timer<br/>based on Server Time.
    UI-->>V: Display Item Page with Live Timer
