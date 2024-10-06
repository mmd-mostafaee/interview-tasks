**Warehouse Management System Design Project**

**Objective:**

Create a RESTful API for a simplified Warehouse Management System (WMS) using Django and Django REST Framework (DRF). The API should support:

1. **Input and Output of Ware**: Adding new products to inventory and removing them when fulfilling orders.
2. **Inventory Tracking and Stock Management**: Keeping track of stock levels and costs using the Ware model.
3. **Cost Handling Strategies**: Implement two strategies for calculating inventory costs:
   - Weighted Mean
   - FIFO (First In, First Out)

**Models Overview:**

1. **Ware Model:**
   - Represents an individual product in the warehouse.
   - Fields:
     - `id` (AutoField)
     - `name` (CharField, unique)
     - `cost_method` (ChoiceField: `weighted_mean`, `fifo`)

2. **Factor Model:**
   - Represents a transaction that can be either an input (restock) or output (fulfillment).
   - Fields:
     - `id` (AutoField)
     - `ware` (ForeignKey to Ware)
     - `quantity` (IntegerField)
     - `purchase_price` (DecimalField, max_digits=10, decimal_places=2)
     - `created_at` (DateTimeField, auto_now_add=True)
     - `type` (ChoiceField: `input`, `output`)
     - `total_cost` (DecimalField, max_digits=10, decimal_places=2)

**Endpoints Overview:**

1. **Create a new Ware**
   - **Endpoint:** `POST /api/wares/`
   - **Description:** This endpoint allows for the creation of new products in the warehouse inventory.
   - **Request Body Example:**
     ```json
     {
       "name": "Widget A",
       "cost_method": "fifo"
     }
     ```
   - **Response:**
     ```json
     {
       "id": 1,
       "name": "Widget A",
       "cost_method": "fifo"
     }
     ```

2. **Add Ware to Inventory (Input Transaction)**
   - **Endpoint:** `POST /api/inventory/input/`
   - **Description:** Adds a specified quantity of a ware to the inventory, updating stock levels and cost.
   - **Request Body Example:**
     ```json
     {
       "ware_id": 1,
       "quantity": 100,
       "purchase_price": 20.00
     }
     ```
   - **Response:**
     ```json
     {
       "factor_id": 1,
       "ware_id": 1,
       "quantity": 100,
       "purchase_price": 20.00,
       "created_at": "2024-10-04T12:34:56Z",
       "type": "input"
     }
     ```

3. **Remove Ware from Inventory (Output Transaction)**
   - **Endpoint:** `POST /api/inventory/output/`
   - **Description:** Removes a specified quantity of ware from inventory to fulfill orders.
   - **Request Body Example:**
     ```json
     {
       "ware_id": 1,
       "quantity": 120
     }
     ```
   - **Response:**
     ```json
     {
       "factor_id": 2,
       "ware_id": 1,
       "quantity": 120,
       "total_cost": 2440.00,
       "created_at": "2024-10-04T13:00:00Z",
       "type": "output"
     }
     ```

4. **Get Total Value of Inventory**
   - **Endpoint:** `GET /api/inventory/valuation/?ware_id=1`
   - **Description:** Retrieves the total value of inventory for a given ware, considering stock levels and cost method.
   - **Response:**
     ```json
     {
       "ware_id": 1,
       "quantity_in_stock": 50,
       "total_inventory_value": 1250.00
     }
     ```

**Example Input and Output:**

**Adding Wares:**
1. **Creating a new Ware:**
   - **API Call:** `POST /api/wares/`
   - **Request Body:**
     ```json
     {
       "name": "Widget A",
       "cost_method": "fifo"
     }
     ```
   - **Response:**
     ```json
     {
       "id": 1,
       "name": "Widget A",
       "cost_method": "fifo"
     }
     ```

**Input Transactions:**
1. **Input Ware for Widget A:**
   - **API Call:** `POST /api/inventory/input/`
   - **Request Body:**
     ```json
     {
       "ware_id": 1,
       "quantity": 100,
       "purchase_price": 20.00
     }
     ```
   - **Response:**
     ```json
     {
       "factor_id": 1,
       "ware_id": 1,
       "quantity": 100,
       "purchase_price": 20.00,
       "created_at": "2024-10-04T12:34:56Z",
       "type": "input"
     }
     ```

**Output Transactions:**
1. **Output Ware for Widget A:**
   - **API Call:** `POST /api/inventory/output/`
   - **Request Body:**
     ```json
     {
       "ware_id": 1,
       "quantity": 120
     }
     ```
   - **Response:**
     ```json
     {
       "factor_id": 2,
       "ware_id": 1,
       "quantity": 120,
       "total_cost": 2440.00,
       "created_at": "2024-10-04T13:00:00Z",
       "type": "output"
     }
     ```

- **FIFO Cost Calculation Example:**
  First, 100 units @ $20.00 = $2000.00  
  Then, 20 units @ $22.00 = $440.00  
  **Total Cost:**  
  100 * 20.00 + 20 * 22.00 = 2440.00

**Total Inventory Valuation:**
1. **Valuation Request:**
   - **API Call:** `GET /api/inventory/valuation/?ware_id=1`
   - **Response:**
     ```json
     {
       "ware_id": 1,
       "quantity_in_stock": 50,
       "total_inventory_value": 1250.00
     }
     ```

**Business Logic:**

1. **Weighted Mean Calculation:**
   - Calculate the average cost of inventory using the formula:
     ```plaintext
     New Average Cost = (Total Cost of Current Stock + Total Cost of New Batch) / Total Quantity in Stock
     ```

2. **FIFO Calculation:**
   - Remove products from inventory starting with the oldest batches first until the requested quantity is fulfilled.
   - Keep track of costs by summing up the costs of the removed transactions.

**Testing Requirements:**

- Implement unit tests for:
  - CRUD operations for Ware and Factor.
  - Input and Output transactions with various edge cases (e.g., insufficient stock).
  - Cost calculations for both `weighted_mean` and `fifo`.
- Use Django’s built-in `TestCase` and `APIClient` for testing.

**Documentation:**

- Create a README file with instructions on setting up the environment, running tests, and making API calls.
