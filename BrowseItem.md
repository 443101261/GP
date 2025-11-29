# Browse / Search Items Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor V as Visitor (Guest)
    participant UI as SearchPage (View)
    participant C as SearchController
    participant S as StatusProcessor (System)
    participant DB as ItemDatabase

    Note over V, UI: 1. User wants to find an item

    V->>UI: Enter Keyword (e.g., "Vintage Watch")
    V->>UI: Click "Search"
    UI->>C: searchRequest(keyword)

    Note over C, S: 2. Include: Compute Item Status
    Note right of C: System ensures no expired items<br/>are shown as "Active".

    C->>S: triggerStatusCheck()
    activate S
    S->>DB: fetchActiveItems()
    activate DB
    DB-->>S: Return Item List
    deactivate DB

    loop For Each Item
        S->>S: Compare Server Time vs End Time
        alt Time Expired
            S->>DB: updateStatus("Closed")
        end
    end
    S-->>C: Status Check Complete
    deactivate S

    Note over C, DB: 3. Fetching the Results

    C->>DB: queryItems(keyword, status="Active")
    activate DB
    DB-->>C: Return Matching Items List
    deactivate DB

    Note over C, UI: 4. Display Results

    C-->>UI: Return Search Results
    UI-->>V: Display Items List (Live Status)
