# Login Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor U as User (Visitor)
    participant UI as LoginPage (View)
    participant C as AuthController
    participant DB_U as UserDatabase
    participant S as AuctionProcessor (System)
    participant DB_I as ItemDatabase

    Note over U, UI: 1. User attempts to Access

    U->>UI: Enter Username & Password
    U->>UI: Click "Login"
    UI->>C: verifyUser(credentials)

    Note over C, DB_U: 2. Authentication Check

    C->>DB_U: checkCredentials(ID, pass)
    activate DB_U
    DB_U-->>C: Credentials Valid / Role: Seller
    deactivate DB_U

    Note over C, S: 3. The "Includes: Compute Status" Logic
    Note right of C: Before showing the Dashboard,<br/>system updates all auctions.

    C->>S: triggerStatusUpdate()
    activate S
    
    S->>DB_I: fetchActiveItems()
    activate DB_I
    DB_I-->>S: List of Items [Item A, Item B]
    deactivate DB_I

    loop For Each Item
        S->>S: Check Server Time vs. End Time
        alt Time Expired
            S->>DB_I: updateStatus("Closed")
        else Time Remaining
            S->>DB_I: updateStatus("Active")
        end
    end
    
    S-->>C: Status Update Complete
    deactivate S

    Note over C, UI: 4. Access Granted

    C-->>UI: Login Success (Redirect to Dashboard)
    UI-->>U: Show Home Screen (With updated prices)
