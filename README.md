# File Management Microservice

This project is a REST API for uploading and managing files. It uses:
- **FastAPI** for the backend logic.
- **MinIO** for storing the actual files (S3 compatible).
- **PostgreSQL** for storing file details (name, size, SHA-256 hash).

## Features
- Upload and Download files.
- Prevents duplicate uploads using SHA-256 hashing.
- View files in the browser.
- Simple web interface included.

## How to Run

1. **Install Docker Desktop** if you haven't already.

2. **Clone this repository**.

3. **Configure Environment**:
   Create a file named `.env` in the project folder and add these exact lines:
   ```ini
   MINIO_ROOT_USER=minioadmin
   MINIO_ROOT_PASSWORD=minioadmin
   POSTGRES_PASSWORD=mysecretpassword
   ```

4. **Start the Project**:
   Open a terminal in the folder and run:
   ```bash
   docker-compose up --build
   ```

## Important Details

### Services & Ports
- **Web API**: `http://localhost:8000` (Main access point)
- **MinIO Console**: `http://localhost:9001` (Storage management)
- **PostgreSQL**: Port `5432` (Database)

### API Documentation
You can test the API endpoints here:
- Swagger UI: `http://localhost:8000/docs`
