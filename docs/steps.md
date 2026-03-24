# Steps to Setup

Follow the steps below **in the given order** to successfully install, configure, and run the Bull system. Each step builds on the previous one, so it is recommended not to skip any stage.

This setup process covers system dependencies, core services, database initialization, and application-level components required for end-to-end functionality.

## Setup Flow

1. **Installation of Python**  
   Install and configure the required Python version.  
   👉 [Setting Up Python](./setup/guide/python.md)

2. **Installation of Docker**  
   Set up Docker for managing containerized services.  
   👉 [Setting Up Docker](./setup/guide/docker.md)

3. **Setting Up Core Services**  
   Install and configure required backend services:
   - Redis
   - NATS
   - MySQL
   - ClickHouse  
   👉 [Setting Up Core Services](./setup/guide/CoreServices.md)

4. **Creating the Blitz Database**  
   Create and initialize the Blitz database schema.  
   👉 [Creating the Blitz Database](./setup/guide/CreateDB.md)

5. **Registering Instruments into Redis**  
   Load and register instrument data into Redis for fast access.  
   👉 [Registering Instruments into Redis](./setup/guide/InstrumentRegister.md)

   - **Installing Python Dependencies (Offline)**  
     Install required Python packages using offline bundles.  
     👉 [Offline Python Dependencies](guide/python-offline-deps.md)

6. **Setting Up Blitz Gateway**  
   Configure and deploy the Blitz Gateway service.  
   👉 [Setting Up Blitz Gateway](guide/blitz-gateway.md)

7. **Setting Up Authentication Server**  
   Configure authentication and authorization services.  
   👉 [Setting Up Auth Server](guide/auth-server.md)

8. **Setting Up Market Data Feed Service**  
   Configure and start the market data feed service for real-time data ingestion.  
   👉 [Setting Up Market Data Feed Service](guide/market-data-feed.md)

Once all steps are completed, the system should be fully operational and ready for validation and testing.