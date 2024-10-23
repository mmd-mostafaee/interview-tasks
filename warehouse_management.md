## Simple Warehouse Management System

### Overview
Design a simple warehouse management system using **Django** and **Django REST Framework (DRF)**. Users can log in, create and manage their own warehouses, wares, and handle transactions such as buying and selling via REST API. Each user should have access to only their own warehouses and associated data. The system should support different costing methods for wares and provide reporting for warehouse activity and ware valuation.

### Requirements

#### 1. **User Authentication**
   - Implement user registration and login using DRF’s token authentication.
   - Each user has their own account and can manage only their data (warehouses, wares, transactions, reports).
   - Proper access control should ensure that users cannot view or modify data belonging to other users.

#### 2. **Warehouse Management**
   - Users can create, update, and delete their own warehouses via REST API endpoints.
   - Each warehouse belongs to a user, and users can have multiple warehouses.
   
#### 3. **Wares (Items) Management**
   - Users can add, update, and delete wares (items) for each warehouse via the API.
   - Each ware has a unique name and belongs to a specific warehouse.
   - Wares must have a defined cost calculation method:
     - **FIFO** (First In, First Out)
     - **Weighted Average Cost**

#### 4. **Transaction Management**
   - Users should be able to manage **purchase orders** and **sales orders** via API:
     - **Purchase Orders (Input Transactions)**: A record of wares being added to the warehouse. The total cost for a purchase order is calculated as `quantity * price per unit`.
     - **Sales Orders (Output Transactions)**: A record of wares being removed from the warehouse. The total cost for sales orders is automatically calculated based on the ware’s costing method and prior transactions.

#### 5. **Costing Methods**
   - **FIFO (First In, First Out)**: When a ware is sold, its cost should be determined based on the oldest available stock first.
   - **Weighted Average Cost**: The cost of a ware is determined as the average cost of all the stock currently available in the warehouse.
   - The system should automatically calculate and track the **cost of goods sold (COGS)** for sales orders based on these methods.

#### 6. **Reports**
   - **Warehouse Activity Report**:
     - Show the history of inputs (purchase orders) and outputs (sales orders) for a warehouse.
     - Include transaction details like quantity and total value for each input and output.
   - **Ware Valuation Report**:
     - Show the current quantity and value of each ware in the warehouse.
     - The value should be calculated according to the ware’s cost method and all previous transactions.

### Constraints and Expectations
1. The system must be built using **Django** and **DRF** to expose a fully functional REST API.
2. Use DRF's token authentication for user management and secure access to the API.
3. Ensure proper database design for efficient transaction management and reporting.
4. Write unit tests for key functionality, including cost calculations, authentication, and reporting.
5. No user interface is required—only API endpoints should be implemented.

### Example Scenarios

#### Scenario 1: FIFO Costing Method

1. **User A** logs in and creates a warehouse called “Central Warehouse” via the API.
2. User A adds a ware called “Widget A” with a costing method set to FIFO.
3. User A creates a purchase order to add **60 units of Widget A** at a price of **$10/unit**.
4. Later, User A creates another purchase order for **50 units of Widget A** at a price of **$12/unit**.
5. User A creates a sales order to sell **80 units of Widget A**. The system should calculate the cost of the sale using FIFO:
   - 60 units at $10/unit
   - 20 units at $12/unit
   - Total cost for the sale is `(60 * $10) + (20 * $12) = $600 + $240 = $840`.
6. User A generates a warehouse activity report, showing both purchase and sales orders, and a ware valuation report showing the remaining **30 units** of Widget A valued at **$12/unit**.

#### Scenario 2: Weighted Average Cost Method

1. **User B** logs in and creates a warehouse called “North Warehouse.”
2. User B adds a ware called “Widget B” with a costing method set to **Weighted Average Cost**.
3. User B creates a purchase order for **100 units of Widget B** at a price of **$15/unit**.
4. Later, User B creates another purchase order for **50 units of Widget B** at a price of **$20/unit**.
5. The weighted average cost of Widget B is calculated as:
   - Total value of inventory: `(100 * $15) + (50 * $20) = $1500 + $1000 = $2500`.
   - Total units: `100 + 50 = 150 units`.
   - Weighted average cost: `$2500 / 150 = $16.67/unit`.
6. User B creates a sales order to sell **80 units of Widget B**. The total cost of the sale is `80 * $16.67 = $1333.60`.
7. User B generates a ware valuation report showing the remaining **70 units** of Widget B valued at **$16.67/unit**.
