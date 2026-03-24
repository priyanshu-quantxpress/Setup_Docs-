

Since the target Linux machine does not have internet access, the Docker image is first built on a Windows machine with internet connectivity, exported as a portable tar file, and then transferred to the Linux server for deployment.

The setup covers building the Docker image, transferring it securely, loading it into the Linux Docker environment, configuring Docker Compose, and running the service as a containerized application. This approach ensures consistent deployments across environments while maintaining isolation, portability, and ease of maintenance.

By following this process, Blitz Gateway can be reliably deployed, restarted, and managed on restricted or secure infrastructure without requiring direct internet access.

## Part 1: Build Docker Image on Windows

###  Go to Blitz Gateway Source Code

On Windows (PowerShell / CMD):

```bash
cd <path-to-blitz-gateway-project>
```

You must have:

- `Dockerfile`
- Application source code
- Config files (if any)

---

###  Build Docker Image

```bash
docker build -t blitz-gateway:latest .
```

Verify image:

```bash
docker images | grep blitz-gateway
```

---

### Save Image as TAR File

```bash
docker save blitz-gateway:latest -o blitz-gateway_latest.tar
```

This file is portable and can be used offline.

---

## Part 2: Transfer Image to Linux Machine

###  Copy Image to Linux

Using **WinSCP**, copy:

```
blitz-gateway_latest.tar
```

To Linux path:

```
/opt/apps/
```

---

## Part 3: Load Image on Linux

### Login to Linux Server

```bash
cd /opt/apps
ls
```

You should see:

```
blitz-gateway_latest.tar
```

---

###  Load Docker Image

```bash
docker load -i blitz-gateway_latest.tar
```

Verify:

```bash
docker images | grep blitz-gateway
```

Expected output:

```
blitz-gateway   latest
```

---

## Part 4: Docker Compose Setup

###  Place Docker Compose File

Ensure the following file exists:

```
/opt/apps/docker-compose.yml
```

Example (simplified):

```yaml
version: "3.8"

services:
  blitz-gateway:
    image: blitz-gateway:latest
    container_name: blitz_gateway
    restart: always
    ports:
      - "8080:8080"
    env_file:
      - .env
    networks:
      - blitz-network

networks:
  blitz-network:
    driver: bridge
```

> ⚠️ Image name must **exactly match** the loaded image (`blitz-gateway:latest`)
> 

---

## Note: Before Running Blitz Gateawy make blow changes at Config Files
```
  "TPOMS": [
  {
    "tpOmsName": "Kite",
    "icons": "PHN2ZyBpZD0iTGF5ZXJfMSIgZGF0YS1uYW1lPSJMYXllciAxIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA5MCA2MCI+PGRlZnM+PHN0eWxlPi5jbHMtMXtmaWxsOiNmNjQ2MWE7fS5jbHMtMntmaWxsOiNkYjM0MmM7fTwvc3R5bGU+PC9kZWZzPjx0aXRsZT5LaXRlIGxvZ28gdHJpbW1lZDwvdGl0bGU+PHBvbHlnb24gY2xhc3M9ImNscy0xIiBwb2ludHM9IjMwIDAgMCAzMCAzMCA2MCA2MCAzMCA5MCAwIDMwIDAiLz48cG9seWdvbiBjbGFzcz0iY2xzLTIiIHBvaW50cz0iMzAgNjAgNjAgMzAgOTAgNjAgMzAgNjAiLz48L3N2Zz4=",
    "selfLogin": true,
    "loginConfig": {
      "loginUrl": "https://kite.zerodha.com/connect/login",
      "tokenUrl": "https://api.kite.trade/session/token"
    },
    "inputs": [
      {
 
        "name": "UserId",
        "label": "User ID",
        "type": "text",
        "required": true,
        "minLength": 3
      },
   {
        "name": "Password",
        "label": "Password",
        "type": "password",
        "required": true
      },
      {
        "name": "ApiKey",
        "label": "API Key",
        "type": "text",
        "required": true
      },
      {
        "name": "ApiSecret",
        "label": "API Secret",
        "type": "password",
        "required": true
      },
   {
        "name": "TotpSecret",
        "label": "TOTP Secret",
        "type": "password",
        "required": true
      },
      {
        "name": "PrimaryIpAddress",
        "label": "Primary IP Address",
        "type": "ipAddress",
        "required": true
      },
      {
        "name": "SecondaryIpAddress",
        "label": "Secondary IP Address",
        "type": "ipAddress",
        "required": true
      },
      {
        "name": "RedirectUrl",
        "label": "Redirect URL",
        "type": "text",
        "required": true,
        "default": "http://localhost:8080/"
      }
    ]
  },
  {
    "tpOmsName": "MOFL",
    "icons": "PHN2ZyB3aWR0aD0iMTAzIiBoZWlnaHQ9IjY0IiB2aWV3Qm94PSIwIDAgNTMgMzIiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+DQogICAgICAgICAgICAgICAgICAgICAgICAgICAgPHBhdGggZD0iTTE2IDMyQzcuMTc3OSAzMiAwIDI0LjgyMjEgMCAxNkMwIDcuMTc3ODkgNy4xNzc5IDAgMTYgMEMyMS42MjgyIDAgMjYuOTA3NCAzLjAxMjExIDI5Ljc3NzMgNy44NjAxN0MyOS45NjA1IDguMTY5NTggMjkuODU4IDguNTY4MjQgMjkuNTQ5MiA4Ljc1MTM3QzI5LjIzOTggOC45MzQ1MSAyOC44NDEyIDguODMyMDMgMjguNjU4IDguNTIzMjhDMjYuMDIwOCA0LjA2ODU5IDIxLjE3MDcgMS4zMDExIDE2IDEuMzAxMUM3Ljg5NTIyIDEuMzAxMSAxLjMwMTExIDcuODk0NTUgMS4zMDExMSAxNkMxLjMwMTExIDI0LjEwNTQgNy44OTQ1NiAzMC42OTg5IDE2IDMwLjY5ODlDMjEuMTcwNyAzMC42OTg5IDI2LjAyMDggMjcuOTMxNCAyOC42NTggMjMuNDc2N0MyOC44NDEyIDIzLjE2NzMgMjkuMjQwNSAyMy4wNjU1IDI5LjU0OTIgMjMuMjQ4NkMyOS44NTg2IDIzLjQzMTggMjkuOTYwNSAyMy44MzA0IDI5Ljc3NzMgMjQuMTM5OEMyNi45MDY3IDI4Ljk4NzkgMjEuNjI3NiAzMiAxNiAzMloiIGZpbGw9InVybCgjcGFpbnQwX2xpbmVhcl82NzM3XzYwNikiPjwvcGF0aD4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICA8cGF0aCBkPSJNNS41NjQ2MyAxNC44MTI3VjE0LjUxNThMOC41NjI4NSAxMy45NTE5VjE1LjMxNzhDOC43ODU2NiAxNC41NDYzIDkuNTcyNCAxMy45NTE5IDEwLjc0NDYgMTMuOTUxOUMxMS42MjA2IDEzLjk1MTkgMTIuMzAyOSAxNC4yOTMgMTIuNTU1NCAxNS4yODhDMTIuNzkyOCAxNC41MTU4IDEzLjU3OTUgMTMuOTUxOSAxNC43MjI2IDEzLjk1MTlDMTUuODY1NyAxMy45NTE5IDE2LjYzNzIgMTQuNDg2MSAxNi42MzcyIDE2LjIyMjlWMjAuMDUyOEMxNi42MzcyIDIwLjMxOTkgMTYuNzI2NSAyMC42NjE3IDE3LjIzMDkgMjAuNjYxN1YyMC45NTg2SDEzLjc4NzFWMjAuNjYxN0MxNC4yOTE2IDIwLjY2MTcgMTQuMzgwOCAyMC4zMjA2IDE0LjM4MDggMjAuMDUyOFYxNi4yODI0QzE0LjM4MDggMTUuNDM2MSAxNC4wODQgMTUuMTM5MyAxMy41Nzk1IDE1LjEzOTNDMTMuMTkzNCAxNS4xMzkzIDEyLjgyMjUgMTUuMzQ2OSAxMi42MTQ5IDE1LjY3MzVDMTIuNjQ0NyAxNS44NTEzIDEyLjY1OTIgMTYuMDI5OCAxMi42NTkyIDE2LjIyMjlWMjAuMDUyOEMxMi42NTkyIDIwLjMxOTkgMTIuNzQ4NSAyMC42NjE3IDEzLjI1MjkgMjAuNjYxN1YyMC45NTg2SDkuODA5MDlWMjAuNjYxN0MxMC4zMTM1IDIwLjY2MTcgMTAuNDAyOCAyMC4zMjA2IDEwLjQwMjggMjAuMDUyOFYxNi4yODI0QzEwLjQwMjggMTUuNDM2MSAxMC4xMDU5IDE1LjEzOTMgOS42MDE0OSAxNS4xMzkzQzkuMTQxMzQgMTUuMTM5MyA4LjY5NTc0IDE1LjQ1MDcgOC41NjIxOSAxNS44ODE3VjIwLjA1MjhDOC41NjIxOSAyMC4zMTk5IDguNjY1OTkgMjAuNjYxNyA5LjE1NTg5IDIwLjY2MTdWMjAuOTU4Nkg1LjcyNzI2VjIwLjY2MTdDNi4yMTcxNiAyMC42NjE3IDYuMzIwOTYgMjAuMzIwNiA2LjMyMDk2IDIwLjA1MjhWMTUuNzQ4MkM2LjMyMDk2IDE1LjI0MzggNi4xODc0MSAxNC45NjE0IDUuNTYzOTYgMTQuODEzNEw1LjU2NDYzIDE0LjgxMjdaIiBmaWxsPSIjMkIyRThDIj48L3BhdGg+DQogICAgICAgICAgICAgICAgICAgICAgICAgICAgPHBhdGggZD0iTTE4LjI3MSAxNy41Mjg1QzE4LjI3MSAxNS41NjkgMTkuMzg0MyAxMy45NTEyIDIxLjkwNzkgMTMuOTUxMkMyNC40MzE0IDEzLjk1MTIgMjUuNTI5NSAxNS41NjkgMjUuNTI5NSAxNy41Mjg1QzI1LjUyOTUgMTkuNDg4MSAyNC40MzE0IDIxLjEwNTkgMjEuOTA3OSAyMS4xMDU5QzE5LjM4NDMgMjEuMTA1OSAxOC4yNzEgMTkuNDg4MSAxOC4yNzEgMTcuNTI4NVpNMjMuMDk1MyAxNy41Mjg1QzIzLjA5NTMgMTQuODQxNyAyMi43OTg0IDE0LjI0OCAyMS45MDc5IDE0LjI0OEMyMS4wMTczIDE0LjI0OCAyMC43MDUzIDE0Ljg0MTcgMjAuNzA1MyAxNy41Mjg1QzIwLjcwNTMgMjAuMjE1NCAyMS4wMTY3IDIwLjgwOTEgMjEuOTA3OSAyMC44MDkxQzIyLjc5OTEgMjAuODA5MSAyMy4wOTUzIDIwLjIxNTQgMjMuMDk1MyAxNy41Mjg1WiIgZmlsbD0iIzJCMkU4QyI+PC9wYXRoPg0KICAgICAgICAgICAgICAgICAgICAgICAgICAgIDxwYXRoIGQ9Ik0yOS43NTkyIDE4LjcwMTRMMzAuMDI2MyAxOC4wNzhMMzIuOTgwMiAxMC41MDgxSDMzLjExMzhMMzYuOTQzNyAxOS40NTg0QzM3LjQwMzkgMjAuNTI3NSAzNy45Njc4IDIwLjY2MSAzOC4zMDk2IDIwLjY2MVYyMC45NTc5SDMzLjQ3MDhWMjAuNjYxQzMzLjk0NTUgMjAuNjYxIDM0LjA5NDMgMjAuMzkzOSAzNC4wOTQzIDIwLjA2NzNDMzQuMDk0MyAxOS43NDA3IDMzLjg1NjkgMTkuMjIxMSAzMy43NTMxIDE4Ljk4MzdMMzMuMzM3MyAxOC4wNzhIMzAuMzIzOEwzMC4xMDEgMTguNzAxNEMyOS43NDQ3IDE5LjYzNjkgMjkuNTk2NiAyMC42NjEgMzEuMjI4OSAyMC42NjFWMjAuOTU3OUgyNy45MzM4VjIwLjY2MUMyOC43NjQ5IDIwLjY2MSAyOS4wNzY5IDIwLjM3ODcgMjkuNzU5OSAxOC43MDE0SDI5Ljc1OTJaTTMxLjc3ODMgMTQuMzIyOEwzMC40Mjc2IDE3Ljc4MTFIMzMuMjE4M0wzMS43NzgzIDE0LjMyMjhaIiBmaWxsPSIjMkIyRThDIj48L3BhdGg+DQogICAgICAgICAgICAgICAgICAgICAgICAgICAgPHBhdGggZD0iTTM4LjYwNjcgMTAuODA0OVYxMC41MDgxSDQyLjUxMDdDNDUuNTUzOCAxMC41MDgxIDQ4LjIxMDMgMTAuOTM4NSA0OC4yMTAzIDEzLjUwNjNDNDguMjEwMyAxNi4wNzQxIDQ1LjU1MzIgMTYuNTA0NSA0Mi41MTA3IDE2LjUwNDVINDIuNDM2NlYxOS40NzNDNDIuNDM2NiAyMC4yMTU0IDQyLjc3NzggMjAuNjYwNCA0My41NSAyMC42NjA0VjIwLjk1NzJIMzguNjA3M1YyMC42NjA0QzM5LjM3ODkgMjAuNjYwNCAzOS43MjA3IDIwLjIxNDggMzkuNzIwNyAxOS40NzNWMTEuOTkxNkMzOS43MjA3IDExLjI0OTIgMzkuMzc5NSAxMC44MDQyIDM4LjYwNzMgMTAuODA0MkwzOC42MDY3IDEwLjgwNDlaTTQyLjUxMDcgMTAuODA0OUg0Mi40MzY2VjE2LjIwNzdINDIuNTEwN0M0My45OTQ5IDE2LjIwNzcgNDUuMjI3MiAxNi4wNzQxIDQ1LjIyNzIgMTMuNTA2M0M0NS4yMjcyIDEwLjkzODUgNDMuOTk0OSAxMC44MDQ5IDQyLjUxMDcgMTAuODA0OVoiIGZpbGw9IiMyQjJFOEMiPjwvcGF0aD4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICA8cGF0aCBkPSJNNDcuNzY1MSAxMC44MDQ5VjEwLjUwODFINTIuNzA3OFYxMC44MDQ5QzUxLjkzNjIgMTAuODA0OSA1MS41OTQ0IDExLjI1MDUgNTEuNTk0NCAxMS45OTIzVjE5LjQ3MzZDNTEuNTk0NCAyMC4yMTYxIDUxLjkzNTYgMjAuNjYxIDUyLjcwNzggMjAuNjYxVjIwLjk1NzlINDcuNzY1MVYyMC42NjFDNDguNTM2NyAyMC42NjEgNDguODc4NSAyMC4yMTU0IDQ4Ljg3ODUgMTkuNDczNlYxMS45OTIzQzQ4Ljg3ODUgMTEuMjQ5OCA0OC41MzczIDEwLjgwNDkgNDcuNzY1MSAxMC44MDQ5WiIgZmlsbD0iIzJCMkU4QyI+PC9wYXRoPg0KICAgICAgICAgICAgICAgICAgICAgICAgICAgIDxkZWZzPg0KICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICA8bGluZWFyR3JhZGllbnQgaWQ9InBhaW50MF9saW5lYXJfNjczN182MDYiIHgxPSI3LjA5NTkyIiB5MT0iMi4zNjYxOCIgeDI9IjI0LjQ3NSIgeTI9IjI4Ljk2NjgiIGdyYWRpZW50VW5pdHM9InVzZXJTcGFjZU9uVXNlIj4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgPHN0b3Agc3RvcC1jb2xvcj0iI0ZFQ0YwQyI+PC9zdG9wPg0KICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICA8c3RvcCBvZmZzZXQ9IjEiIHN0b3AtY29sb3I9IiNGQUFEMUMiPjwvc3RvcD4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgPC9saW5lYXJHcmFkaWVudD4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICA8L2RlZnM+DQogICAgICAgICAgICAgICAgICAgICAgICA8L3N2Zz4=",
    "selfLogin": false,
    "inputs": [
      {
        "name": "ClientId",
        "label": "Client ID",
        "type": "text",
        "required": true,
        "minLength": 3
      },
      {
        "name": "Password",
        "label": "Password",
        "type": "password",
        "required": true
      },
      {
        "name": "ApiKey",
        "label": "API Key",
        "type": "text",
        "required": true
      },
      {
        "name": "PrimaryIpAddress",
        "label": "Primary IP Address",
        "type": "ipAddress",
        "required": true
      },
      {
        "name": "SecondaryIpAddress",
        "label": "Secondary IP Address",
        "type": "ipAddress",
        "required": true
      },
      {
        "name": "DOB",
        "label": "Date of Birth",
        "type": "date",
        "required": true,
        "minLength": 10,
        "maxLength": 10
      }
    ]
  },
  {
    "tpOmsName": "XTS",
    "icons": "",
    "selfLogin": true,
    "inputs": [
      {
        "name": "UserId",
        "label": "User ID",
        "type": "text",
        "required": true,
        "minLength": 3
      },
      {
        "name": "BaseURL",
        "label": "BaseURL",
        "type": "text",
        "required": true
      },
      {
        "name": "ApiKey",
        "label": "API Key",
        "type": "text",
        "required": true
      },
   {
        "name": "ApiSecret",
        "label": "Api Secret",
        "type": "text",
        "required": true
      },
    {
        "name": "TotpSecret",
        "label": "Totp Secret",
        "type": "text",
        "required": false
      },
   {
        "name": "Source",
        "label": "Source",
        "type": "text",
        "required": true
      },
   {
        "name": "InteractivePath",
        "label": "Interactive Path",
        "type": "text",
        "required": true
      }
    ]
  }
]

```


## Part 5: Run Blitz Gateway

###  Start the Service

```bash
docker-compose up -d
```

---

###  Verify Container

```bash
docker ps | grep blitz_gateway
```

Expected output:

```
Up ... blitz_gateway
```

---

## Part 6: Validate Gateway

### Check Logs

```bash
docker logs -f blitz_gateway
```