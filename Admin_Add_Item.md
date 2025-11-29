# Admin Add Item Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor A as Admin
    participant UI as AdminDashboard (View)
    participant C as ItemManager (Controller)
    participant V as DataValidator (System)
    participant IMG as ImageServer (System)
    participant DB as ItemDatabase

    Note over A, UI: 1. Admin lists a special item

    A->>UI: Select "Add Auction Item"
    A->>UI: Enter Details (Title, Reserve Price)
    A->>UI: Upload Official Images & Click "Publish"
    
    UI->>C: submitAdminListing(data, images)

    Note over C, V: 2. Include: Validate Item Data
    Note right of C: System checks format ensuring<br/>standardization (PDF Goal).

    C->>V: validateData(price, description)
    activate V
    V-->>C: Data Valid
    deactivate V

    Note over C, IMG: 3. Include: Upload Images
    
    C->>IMG: uploadFiles(image_data)
    activate IMG
    IMG->>IMG: Process & Store High-Res
    IMG-->>C: Return Image URLs
    deactivate IMG

    Note over C, DB: 4. Saving the Item

    C->>DB: createItem(info, URLs, type="Admin/Featured")
    activate DB
    DB-->>C: Item Created (ID: 900)
    deactivate DB

    Note over C, UI: 5. Success

    C-->>UI: Special Auction Created
    UI-->>A: Show Item in "Featured" List
