flowchart TD
  Start[Start] --> Auth[Authentication]
  Auth --> C{Authenticated?}
  C -->|yes| Dashboard[Dashboard]
  C -->|no| Auth
  Dashboard --> BOM[BOM Management]
  Dashboard --> Inventory[Inventory Management]
  Dashboard --> Production[Production Workflow]
  BOM --> BOMList[List BOMs]
  BOM --> BOMCreate[Create BOM]
  BOMList --> BOMDetail[BOM Details]
  BOMCreate --> APIBOMPost[API Post BOM]
  Inventory --> InventoryList[List Inventory]
  Inventory --> InventoryUpdate[Update Inventory]
  InventoryList --> APIInventoryGet[API Get Inventory]
  InventoryUpdate --> APIInventoryPost[API Post Inventory]
  Production --> WorkOrderList[List Work Orders]
  Production --> IssueMaterial[Issue Material]
  WorkOrderList --> APIProductionGet[API Get Production]
  IssueMaterial --> APIProductionPost[API Issue Material]
  APIBOMPost --> DB[Database]
  APIInventoryGet --> DB
  APIInventoryPost --> DB
  APIProductionGet --> DB
  APIProductionPost --> DB