
Follow the steps below **in the given order** to successfully install, configure, and run the Bull system. Each step builds on the previous one, so it is recommended not to skip any stage.

This setup process covers system dependencies, core services, database initialization, and application-level components required for end-to-end functionality.

## Setup Flow

1. **Installation of Python**  
   Install and configure the required Python version along with essential system tools.
    👉 [Setting Up Python](guide/python.md)

2. **Installation of Docker**  
   Set up Docker to manage containerized services used by the system.

3. **Setting Up Core Services**  
   Install and configure the following services:
   - Redis
   - NATS
   - MySQL
   - ClickHouse

4. **Creating the Blitz Database**  
   Initialize the required database schema and core tables used by the system.

5. **Registering Instruments into Redis**  
   Load and register instrument data into Redis for fast access and caching.
   
   - **Installing Python Dependencies (Offline)**  
     Install all required Python packages using offline dependency bundles.

6. **Setting Up Blitz Gateway**  
   Configure and deploy the Blitz Gateway service for request routing and system communication.

7. **Setting Up Authentication Server**  
   Install and configure the authentication service for user and service-level access control.

8. **Setting Up Market Data Feed Service**  
   Configure and start the market data feed service to enable real-time data ingestion and processing.

Once all steps are completed, the system should be fully operational and ready for validation and testing.