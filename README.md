# MinIO File Management Microservice

A high-performance, containerized REST microservice for managing file uploads, downloads, and previews using **FastAPI**, **MinIO**, and **PostgreSQL**.

## 🚀 Features

- **Store Any File**: Securely upload and store textual and binary files.
- **Smart Metadata**: Automatically extracts and stores metadata (size, MIME type, SHA-256 hash).
- **Direct Streaming**: Stream files directly from MinIO to the client without heavy memory usage.
- **Instant Previews**: View supported files (images, PDFs) directly in the browser.
- **Duplicate Prevention**: SHA-256 hashing ensures data integrity and effective management.
- **Web Interface**: Simple, built-in HTML interface for testing and interaction.

## 🛠️ Tech Stack

- **Backend**: Python 3.10, FastAPI, SQLModel (Async)
- **Object Storage**: MinIO (S3 compatible)
- **Database**: PostgreSQL 15 (AsyncPG)
- **Infrastructure**: Docker & Docker Compose

## 📋 Prerequisites

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)

## ⚡ Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-folder>
```

### 2. Configure Environment

Create a `.env` file in the root directory. You can use the values below as a starting point:

```ini
# MinIO Configuration
MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=minioadmin

# Database Configuration
POSTGRES_PASSWORD=mysecretpassword
```

### 3. Run with Docker

Start the entire stack with a single command:

```bash
docker-compose up -d --build
```

The services will start on the following ports:
- **API**: `http://localhost:8000`
- **MinIO Console**: `http://localhost:9001`
- **PostgreSQL**: `5432`

## 🖥️ Usage

### Web Interface
Visit `http://localhost:8000` to access the built-in file manager. You can upload files and see the list of stored files.

### API Documentation
Interactive API documentation is automatically generated:
- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

### Key Endpoints

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/files/upload` | Upload a new file |
| `GET` | `/files` | List all files with metadata |
| `GET` | `/files/{id}` | Preview/Stream a file |
| `GET` | `/files/{id}/download` | Force download a file |
| `DELETE` | `/files/{id}` | Delete a file permanently |

## 🏗️ Architecture

![Architecture Diagram](https://mermaid.ink/img/pako:eNptkMFOwzAMhl_F8gmp4wV44LQTQ0wIt01IHC6tW9XYSRwUBap3x0m7DozT_Pnz2_9fMsolzZARRWfrWAc9iwvG-1N2BC-WiyfQcArfUIdq-74_gO_BwRkc4RMa0EMD1-B78DAA9-DjCDrwz2_BwzN4eAMdDLAG39_AFRzAAHvw_RNcwQ1s4ADW4PsXuIIH2MABbMH3L3AFn6dp-vQ8Td_e0_TjI03fP9L04zNNP7_S9OsvTb_9pukP_4x-h2XG2JgS1sFaU0Jjra0l42OOS_bRejLKeGusNfaYom-t9XRYj4yx1lqbz9F7a63N5-iDtTY_v_QxRe-ttTZ_fvlDdH5-A6pIf0E)

### Services

1.  **API Services (FastAPI)**
    *   **Rôle**: Point d'entrée unique pour le client. Gère la logique métier, la validation, et l'orchestration.
    *   **Technologies**: Python 3.10, FastAPI, Uvicorn, SQLModel.
    *   **Port interne**: 8000.

2.  **Stockage Objet (MinIO)**
    *   **Rôle**: Stockage persistant et performant des fichiers binaires. Compatible S3.
    *   **Configuration**: Persistance locale via volume Docker `minio_data`.
    *   **Ports**: 9000 (API), 9001 (Console).

3.  **Base de Données (PostgreSQL)**
    *   **Rôle**: Stockage structuré des métadonnées des fichiers (taille, hash, type mime, dates).
    *   **Mode**: Asynchrone (AsyncPG) pour des performances non-bloquantes avec FastAPI.
    *   **Port**: 5432.

### FLux de Données

#### 1. Upload de Fichier
Lorsqu'un utilisateur envoie un fichier sur `POST /files/upload` :
1.  L'API reçoit le flux de données.
2.  Elle calcule à la volée le hash **SHA-256** et la taille du fichier.
3.  Elle transmet le fichier au service **MinIO** avec un nom unique (UUID).
4.  Une fois le stockage confirmé, elle enregistre les métadonnées dans **PostgreSQL**.
5.  Elle retourne l'objet métadonnée complet au client.

#### 2. Téléchargement (Download)
Lors d'une requête `GET /files/{id}` :
1.  L'API vérifie l'existence de l'ID dans **PostgreSQL**.
2.  Elle récupère le nom de l'objet MinIO associé.
3.  Elle ouvre un flux de lecture depuis **MinIO**.
4.  Elle stream la réponse directement au client (StreamingResponse) pour minimiser l'usage mémoire.

### Modèle de Données (FileMetadata)

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Clé primaire unique |
| `filename` | String | Nom original du fichier |
| `size` | Integer | Taille en octets |
| `content_type` | String | Type MIME (ex: image/png) |
| `hash` | String | Empreinte SHA-256 pour intégrité |
| `upload_date` | DateTime | Timestamp d'upload |
| `minio_object_name` | String | Identifiant interne dans MinIO |
