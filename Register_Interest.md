# Register Interest Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor S as Seller (User)
    participant UI as ItemPage (View)
    participant C as InterestController
    participant V as RegistrationValidator (System)
    participant DB as UserDatabase

    Note over S, UI: 1. User wants to follow an item

    S->>UI: Select Item #550
    S->>UI: Click "Register Interest" (Watch/Notify)
    UI->>C: submitInterest(userID, itemID)

    Note over C, V: 2. Include: Validate Registration
    Note right of C: System checks if the user is<br/>verified to participate.

    C->>V: checkUserStatus(userID)
    activate V
    V->>DB: fetchUserDetails(userID)
    activate DB
    DB-->>V: Return Status (Verified/Active)
    deactivate DB

    alt User Not Verified
        V-->>C: Error: Registration Incomplete
        C-->>UI: Show "Please verify account first"
    else User Verified
        V-->>C: Validation Passed
        deactivate V
        
        Note over C, DB: 3. Save Interest
        C->>DB: addWatchlist(userID, itemID)
        activate DB
        DB-->>C: Saved Successfully
        deactivate DB

        Note over C, UI: 4. Confirmation
        C-->>UI: Success: You are watching this item
        UI-->>S: Update Button to "Registered"
    end
