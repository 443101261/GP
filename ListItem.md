# Create / List Item Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor S as Seller
    participant UI as ListingPage (View)
    participant C as ItemManager (Controller)
    participant V as DataValidator (System)
    participant IMG as ImageServer (System)
    participant DB as ItemDatabase

    Note over S, UI: 1. Seller starts listing an item

    S->>UI: Click "Sell Item"
    UI-->>S: Show Item Details Form
    S->>UI: Enter Title, Price, Description
    S->>UI: Select Images & Click "Post Listing"

    Note over UI, C: 2. System Processing

    UI->>C: submitListing(data, images)
    activate C

    Note right of C: Include 1: Validate Item Data
    
    C->>V: validateData(price, description)
    activate V
    Note right of V: Checks for "Unclear Pricing"<br/>or "Fake Listings" (PDF Goal)
    V-->>C: Data Validated
    deactivate V

    Note right of C: Include 2: Upload Images
    
    C->>IMG: uploadFiles(image_data)
    activate IMG
    IMG->>IMG: Compress & Store
    IMG-->>C: Return Image URLs
    deactivate IMG

    Note over C, DB: 3. Saving the Auction

    C->>DB: createItemRecord(info, URLs, status="Active")
    activate DB
    DB-->>C: Item Saved (ID: 550)
    deactivate DB

    Note over C, S: 4. Confirmation

    C-->>UI: Listing Created Successfully
    deactivate C
    UI-->>S: Show "Item is now Live"
