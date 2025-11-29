# Delete Listing Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor S as Seller
    participant UI as DashboardPage (View)
    participant C as ItemManager (Controller)
    participant B as BidSystem (System)
    participant DB as ItemDatabase

    Note over S, UI: 1. Seller wants to remove an item

    S->>UI: Select Item #550
    S->>UI: Click "Delete Listing"
    UI->>C: requestDelete(itemID: 550)

    Note over C, B: 2. Check for Active Bids
    Note right of C: The "Per Bid" Logic: <br/>Cannot delete if bids exist.

    C->>B: checkActiveBids(itemID)
    activate B
    B->>DB: countBids(itemID)
    activate DB
    DB-->>B: Return Bid Count
    deactivate DB
    B-->>C: Result (Bids = 0 OR Bids > 0)
    deactivate B

    Note over C, UI: 3. Decision Logic

    alt Active Bids Exist (Count > 0)
        C-->>UI: Error: "Cannot delete. Active bids exist."
        UI-->>S: Display Error Message
    else No Bids (Count = 0)
        C->>DB: deleteItem(itemID)
        activate DB
        DB-->>C: Item Deleted
        deactivate DB
        C-->>UI: Success: Item Removed
        UI-->>S: Update Dashboard (Item Gone)
    end
