# File Management Microservice

This project is a REST API for uploading and managing files. It uses:
- **FastAPI** for the backend logic.
- **MinIO** for storing the actual files (S3 compatible).
- **PostgreSQL** for storing file details.

## Features
- Upload and Download files.
- Prevents duplicate uploads using SHA-256 hashing.
- **Direct Preview**: View files like Images and PDFs directly in the browser without downloading.

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

## Technical Details

### Database Storage
We store the following metadata for each file in PostgreSQL:
- **Filename**: Original name of the file.
- **Size**: Size in bytes.
- **Content Type**: MIME type (e.g., `image/png`).
- **Hash**: SHA-256 checksum (to find duplicates).
- **MinIO Object Name**: The ID used to retrieve the file from storage.

### File Previews
The system supports **streaming responses**, allowing you to view these file types directly in the browser:
- Images (PNG, JPEG, GIF, etc.)
- PDF Documents
- Text Files
- Any other browser-supported media

### Services & Ports
- **Web API**: `http://localhost:8000` (Main access point)
- **MinIO Console**: `http://localhost:9001` (Storage management)
- **PostgreSQL**: Port `5432` (Database)

### API Documentation
You can test the API endpoints here:
- Swagger UI: `http://localhost:8000/docs`
