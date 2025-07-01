# Customer Journey Data Flow - Mermaid Version

```mermaid
flowchart TD
    %% Main customer journey processes
    Browse["1. Browse & Search"]
    Cart["2. Shopping Cart"]
    Checkout["3. Checkout Process"]
    Orders["4. Order Tracking"]
    Service["5. Customer Service"]
    
    %% Data stores
    ProductDB[(Product Database)]
    CustomerDB[(Customer Database)]
    OrderDB[(Order Database)]
    
    %% External systems
    Payment[Payment System]
    Shipping[Shipping System]
    
    %% Main flow
    Browse --> Cart --> Checkout --> Orders
    Orders --> Service
    
    %% Data connections
    ProductDB --> Browse
    ProductDB --> Cart
    
    CustomerDB --> Cart
    CustomerDB <--> Checkout
    CustomerDB <--> Service
    
    Checkout --> OrderDB
    OrderDB --> Orders
    OrderDB --> Service
    
    Checkout <--> Payment
    Checkout <--> Shipping
    Orders <--> Shipping
    
    %% Style
    classDef process fill:#f9f,stroke:#333,stroke-width:2px;
    classDef database fill:#bfb,stroke:#3b3,stroke-width:2px;
    classDef external fill:#bbf,stroke:#33f,stroke-width:2px;
    
    class Browse,Cart,Checkout,Orders,Service process;
    class ProductDB,CustomerDB,OrderDB database;
    class Payment,Shipping external;
```

## Data Flow Description

### 1. Browse & Search
- **Input**: Customer search queries, category selections
- **Output**: Product listings, details, images, prices
- **Data Source**: Product Database
- **Key Actions**: View products, filter results, compare items

### 2. Shopping Cart
- **Input**: Product selections, quantity changes
- **Output**: Cart contents, subtotals, available discounts
- **Data Source**: Product Database, Customer Database
- **Key Actions**: Add/remove items, update quantities, apply coupons

### 3. Checkout Process
- **Input**: Shipping address, payment details
- **Output**: Order confirmation, receipt
- **Data Destination**: Customer Database, Order Database
- **External Systems**: Payment gateway, tax calculator
- **Key Actions**: Select shipping method, make payment, place order

### 4. Order Tracking
- **Input**: Order ID, customer account
- **Output**: Order status, shipping updates
- **Data Source**: Order Database
- **External Systems**: Shipping carrier
- **Key Actions**: View order status, download invoice, request returns

### 5. Customer Service
- **Input**: Support requests, product questions
- **Output**: Support responses, return authorizations
- **Data Source**: Order Database, Customer Database
- **Key Actions**: Contact support, submit return requests, leave reviews
