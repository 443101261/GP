# Admin Registrations Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor A as Admin
    participant UI as AdminDashboard (View)
    participant C as UserManager (Controller)
    participant V as RegistrationValidator (System)
    participant DB as UserDatabase

    Note over A, UI: 1. Admin reviews pending users

    A->>UI: View "Pending Registrations"
    UI->>C: fetchPendingUsers()
    C->>DB: query(status="Pending")
    DB-->>UI: Return List [User A, User B]
    
    A->>UI: Select User #101 & Click "Verify"

    Note over C, V: 2. Include: Validate Registration
    Note right of C: System re-checks data before<br/>allowing Admin approval.

    UI->>C: processVerification(userID: 101)
    C->>V: validateUserDocuments(userID)
    activate V
    
    V->>DB: fetchUploadedIDs(userID)
    DB-->>V: Return ID Images
    V->>V: Auto-Check Format/Expiry
    V-->>C: Documents Valid
    deactivate V

    Note over A, UI: 3. Final Approval

    C-->>UI: Show "Documents Valid - Approve?"
    A->>UI: Click "Approve User"
    
    UI->>C: setStatus(userID, "Verified")
    activate C
    C->>DB: updateRecord(userID, status="Verified")
    activate DB
    DB-->>C: Success
    deactivate DB
    
    C-->>UI: User Approved Successfully
    deactivate C
