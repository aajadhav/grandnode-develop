# GrandNode Infrastructure Diagram

```mermaid
flowchart TD
    %% Client Side
    subgraph Clients["Client Devices"]
        Browser["Web Browser"]
        Mobile["Mobile Device"]
        ThirdParty["Third-Party System"]
    end
    
    %% Load Balancing
    subgraph LoadBalancing["Load Balancing"]
        LB["Load Balancer"]
        CDN["Content Delivery Network"]
    end
    
    %% Web Servers
    subgraph WebServers["Web Server Layer"]
        WebServer1["Web Server 1"]
        WebServer2["Web Server 2"]
        WebServerN["Web Server N"]
    end
    
    %% Application Servers
    subgraph AppServers["Application Server Layer"]
        AppServer1["App Server 1"]
        AppServer2["App Server 2"]
        AppServerN["App Server N"]
    end
    
    %% Caching Layer
    subgraph CachingLayer["Caching Layer"]
        Redis["Redis Cache Cluster"]
        OutputCache["Output Cache"]
        DataCache["Data Cache"]
    end
    
    %% Database Layer
    subgraph DatabaseLayer["Database Layer"]
        MongoDB["MongoDB Cluster"]
        subgraph MongoNodes["MongoDB Nodes"]
            Primary["Primary Node"]
            Secondary1["Secondary Node 1"]
            Secondary2["Secondary Node 2"]
        end
        Backup["Backup System"]
    end
    
    %% Storage Layer
    subgraph StorageLayer["Storage Layer"]
        FileStorage["File Storage"]
        MediaServer["Media Server"]
        BlobStorage["Blob Storage"]
    end
    
    %% External Services
    subgraph ExternalServices["External Services"]
        PaymentGateways["Payment Gateways"]
        ShippingAPIs["Shipping APIs"]
        EmailService["Email Service"]
        SMSService["SMS Service"]
        TaxService["Tax Service"]
    end
    
    %% Monitoring & Management
    subgraph MonitoringSystem["Monitoring & Management"]
        Logging["Logging System"]
        Metrics["Metrics Collection"]
        Alerts["Alert System"]
        Dashboard["Monitoring Dashboard"]
    end
    
    %% Security Layer
    subgraph SecurityLayer["Security Layer"]
        Firewall["Firewall"]
        WAF["Web Application Firewall"]
        DDOS["DDoS Protection"]
        SSL["SSL/TLS Termination"]
    end
    
    %% Connections
    Clients --> SecurityLayer
    SecurityLayer --> LoadBalancing
    LoadBalancing --> WebServers
    WebServers --> AppServers
    AppServers --> CachingLayer
    AppServers --> DatabaseLayer
    AppServers --> StorageLayer
    AppServers --> ExternalServices
    
    %% Specific connections
    Browser --> LB
    Mobile --> LB
    ThirdParty --> LB
    
    LB --> WebServer1
    LB --> WebServer2
    LB --> WebServerN
    
    CDN --> Browser
    CDN --> Mobile
    
    WebServer1 --> AppServer1
    WebServer2 --> AppServer2
    WebServerN --> AppServerN
    
    AppServer1 --> Redis
    AppServer2 --> Redis
    AppServerN --> Redis
    
    AppServer1 --> MongoDB
    AppServer2 --> MongoDB
    AppServerN --> MongoDB
    
    MongoDB --> Primary
    Primary --> Secondary1
    Primary --> Secondary2
    MongoDB --> Backup
    
    AppServer1 --> FileStorage
    AppServer2 --> FileStorage
    AppServerN --> FileStorage
    
    AppServer1 --> PaymentGateways
    AppServer1 --> ShippingAPIs
    AppServer1 --> EmailService
    
    %% Monitoring connections
    WebServers --> Logging
    AppServers --> Logging
    DatabaseLayer --> Logging
    
    WebServers --> Metrics
    AppServers --> Metrics
    DatabaseLayer --> Metrics
    CachingLayer --> Metrics
    
    Metrics --> Alerts
    Logging --> Alerts
    Alerts --> Dashboard
    
    %% Style definitions
    classDef clients fill:#f9d5e5,stroke:#333,stroke-width:1px;
    classDef security fill:#ff9999,stroke:#333,stroke-width:1px;
    classDef loadBalancing fill:#eeac99,stroke:#333,stroke-width:1px;
    classDef webServers fill:#e06377,stroke:#333,stroke-width:1px;
    classDef appServers fill:#c83349,stroke:#333,stroke-width:1px;
    classDef caching fill:#5b9aa0,stroke:#333,stroke-width:1px;
    classDef database fill:#d6e5fa,stroke:#333,stroke-width:1px;
    classDef storage fill:#f9cb9c,stroke:#333,stroke-width:1px;
    classDef external fill:#b6d7a8,stroke:#333,stroke-width:1px;
    classDef monitoring fill:#d9d2e9,stroke:#333,stroke-width:1px;
    
    class Clients,Browser,Mobile,ThirdParty clients;
    class SecurityLayer,Firewall,WAF,DDOS,SSL security;
    class LoadBalancing,LB,CDN loadBalancing;
    class WebServers,WebServer1,WebServer2,WebServerN webServers;
    class AppServers,AppServer1,AppServer2,AppServerN appServers;
    class CachingLayer,Redis,OutputCache,DataCache caching;
    class DatabaseLayer,MongoDB,MongoNodes,Primary,Secondary1,Secondary2,Backup database;
    class StorageLayer,FileStorage,MediaServer,BlobStorage storage;
    class ExternalServices,PaymentGateways,ShippingAPIs,EmailService,SMSService,TaxService external;
    class MonitoringSystem,Logging,Metrics,Alerts,Dashboard monitoring;
```

## Infrastructure Components Description

### Client Devices
- **Web Browser**: Desktop and mobile browsers accessing the web store
- **Mobile Device**: Native mobile applications
- **Third-Party System**: External systems integrating via API

### Security Layer
- **Firewall**: Network security system monitoring and controlling incoming/outgoing traffic
- **Web Application Firewall**: Filters, monitors, and blocks HTTP traffic
- **DDoS Protection**: Mitigates distributed denial-of-service attacks
- **SSL/TLS Termination**: Handles encryption/decryption of secure connections

### Load Balancing
- **Load Balancer**: Distributes incoming network traffic across multiple servers
- **Content Delivery Network**: Geographically distributed network for fast content delivery

### Web Server Layer
- **Web Servers**: Handles HTTP requests, serves static content, and forwards dynamic requests

### Application Server Layer
- **App Servers**: Runs the GrandNode application, processes business logic

### Caching Layer
- **Redis Cache Cluster**: In-memory data structure store for high-performance caching
- **Output Cache**: Caches rendered HTML pages and partial views
- **Data Cache**: Caches database query results and application objects

### Database Layer
- **MongoDB Cluster**: NoSQL document database for storing application data
- **MongoDB Nodes**: Primary and secondary nodes for high availability
- **Backup System**: Regular data backups for disaster recovery

### Storage Layer
- **File Storage**: Stores product images, documents, and other media files
- **Media Server**: Optimizes and serves media content
- **Blob Storage**: Stores large binary objects

### External Services
- **Payment Gateways**: Processes credit card and alternative payment methods
- **Shipping APIs**: Calculates shipping rates and generates shipping labels
- **Email Service**: Sends transactional and marketing emails
- **SMS Service**: Sends text message notifications
- **Tax Service**: Calculates sales tax based on location

### Monitoring & Management
- **Logging System**: Collects and stores application logs
- **Metrics Collection**: Gathers performance metrics
- **Alert System**: Notifies administrators of issues
- **Monitoring Dashboard**: Visualizes system health and performance
