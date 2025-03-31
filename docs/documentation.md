# McMap
McMap is a web application that visualizes all McDonald's outlets in Kuala Lumpur, Malaysia. It features an interactive map that details if an outlet is near another within 5km, and a chatbot to help users find specific outlets.

## Introduction
McMap is a full-stack web application that allows users to:  
1. **Find McDonald's outlets** via chatbot queries.  
2. **View outlets on an interactive map** with 5KM radius overlays.  
3. **Check overlapping service areas** for better coverage visualization.

### Tech Stack
- **Scraping:** Python
-   **Backend:** FastAPI, SQLite
    
-   **Frontend:** Vite+React
    
-   **Hosting:** Railway (Backend and Frontend)
    
-   **Map:** Leaflet.js

## Setup Instructions
### Scraping
1. Clone the repository:

```bash
git clone https://github.com/Falachi/McMapScraping.git
```
2. Run the file.
```bash
python scrape-mcd.py
```
3. The data will be in `mcd_outlets.db` and `mcd_outlets.json`.
### Backend
**Local Setup**
1. Clone the repository:

```bash
git clone https://github.com/Falachi/McMapAPI.git
```

2. Navigate to the project directory:

```bash
cd McMapAPI
```

3. Create and activate a virtual environment:

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

4. Install dependencies:

```bash
pip install -r requirements.txt
```

5. Create a `.env` folder and put in the allowed origin URL.
```
ALLOWED_ORIGIN={YOUR URL HERE}
```
6. Run the FastAPI server:
```bash
uvicorn main:app --host 127.0.0.1 --port 8000
```
7. Open API docs: http://127.0.0.1:8000/docs

**Deployment (Railway)**
Currently hosted at: https://mcmapapi-production.up.railway.app
### Frontend (Vite + React + TS)
**Local Setup**
1. Clone the Repository

```bash
git clone https://github.com/Falachi/McMap.git
cd McMap
```

2. Install Dependencies

```bash
npm install
```

3. Create a `.evn` file in the root directory and add:

```bash
VITE_API_URL=http://127.0.0.1:8000
```

Replace `http://127.0.0.1:8000` with your backend URL.

4. Run the application

```bash
npm run dev
```
5. Open at: http://localhost:5173/

**Deployment (Railway)**
Currently hosted at: https://mcmap.falachi.me

## API Documentation
### Endpoints
| Method | Endpoint | Description |Parameters|Returns
|--|--|--|--|--|
|`get`|`/outlets`|Get all available outlets. | None | `json`
|`get`|`/outlets/search`|Get outlets based on a keyword. | `String` (query)| `json`
|`get`|`/outlets/category/location`| Get outlets based on categories and location. | `String` (query) `String` (query)| `json`
|`get`|`/outlets/location/{location}`|Get outlets based on a location. | `String` path| `json`
|`get`|`/outlets/nearby`|Get outlets based on a latitude, longitude and radius search. | `number` (query) `number` (query) `number` (query) | `json`
|`get`|`/outlets/{outlet_id}`|Get the outlet based on the ID. | `integer` path| `json`
|`get`|`/outlets/category/{category}`|Get the outlet outlets by categories. | `array<any>`| `json`
|`get`|`("/outlets/{outlet_id}/services`|Get the services offered by a specific outlet. | `integer` path| `json`
|`get`|`/chatbot/query`|Get the chatbot response. | `String` query| `json`
### Example
**Get all outlets**
``` bash
GET https://mcmapapi-production.up.railway.app/outlets
```
**Response**
```json
{
  "outlets": [
    {
      "id": 1,
      "name": "McDonald's Bukit Bintang",
      "address": "120-120A Jalan Bukit Bintang, 55100, Kuala Lumpur, Malaysia",
      "telephone": "03-21427843",
      "email": "s001@my.mcd.com",
      "fax": "03-21418009",
      "lat": 3.146847,
      "lng": 101.710931
    },
    {
      "id": 2,
      "name": "McDonald's Bangsar",
      "address": "48, Jalan Telawi Lima, 59100, Bangsar Baru, Bangsar, Kuala Lumpur, Malaysia",
      "telephone": "03-22012551",
      "email": "s008@my.mcd.com",
      "fax": "03-22843790",
      "lat": 3.13295,
      "lng": 101.672297
    }
  ]
}
```
## Solutions and Technical Decisions
The focus on the technology stack is lightweight and efficient, ensuring that the backend remains easy to deploy, scalable, and cost-effective.

### 1. Scraping: Python

**Decision:** The scraping was done with Python.

**Reasoning**
The [McDonald's locate us](https://www.mcdonalds.com.my/locate-us) page was found to send a `post` request to `https://www.mcdonalds.com.my/storefinder/index.php` with the appropriate parameters (action, state, and address) which then returns a `json` with the outlets and other information. As such, I decided to retrieve the information with a payload that request only outlets in Kuala Lumpur. The outlets are then extracted from the `json` and saved into a `SQLite` database and `json` file.

**Additional Information** 
-   Versatility – Python supports various scraping methods, including direct HTTP requests and JavaScript-rendered content handling.
-   Code simplicity – Python's readability and built-in support for data manipulation make it an ideal choice for scraping tasks.- Easy integration – Works seamlessly with SQLite.

### 1. Backend: FastAPI

**Decision:** The backend was built using FastAPI.

**Reasoning**
The backend was developed using **FastAPI**, a modern, high-performance web framework for building APIs with Python. The endpoints are created depending on possible requirements in the frontend. The chatbot query is processed by:
1. Removing any noise words such as "what", "the", "and".
2. Extract the categories and location with **Fuzzy Matching**.
3. Get the outlets that fit with the categories and location.
4. Create the reply message.
5. Sends the message back to the client.

**Chatbot**
The chatbot utilizes **Fuzzy Matching** to identify relevant keywords and **Natural Language Processing (NLP)** techniques for preprocessing user queries. This approach is lightweight, fast, and does not require large-scale AI models, making it suitable for the project's scope.

However, there are some limitations:
-   **Limited understanding of complex queries** – The chatbot does not perform deep semantic analysis, meaning it may struggle with multi-layered or ambiguous questions.
    
-   **No support for negation ("not", "without")** – The system does not explicitly handle negations, which may lead to incorrect or incomplete responses.
    
-   **Fixed keyword-based processing** – The chatbot relies on predefined keyword matching rather than contextual interpretation, which can limit flexibility.
    
-   **Lack of conversational memory** – Each query is processed independently without remembering previous interactions.

**Additional Information**  
- Asynchronous and fast – Built on Starlette and Pydantic, making API calls efficient.  
- Automatic documentation – Generates Swagger UI (`/docs`) and ReDoc (`/redoc`) automatically.  
- Easy to Use - Simple syntax to create endpoints fast.
- Easy integration – Works seamlessly with SQLite.

### 2. Database: SQLite

**Decision:** The project uses an SQLite database (`mcd_outlets.db`) to store McDonald's outlets.

**Reasoning**
SQLite was chosen due to its lightweight nature and native support in Python. Since the dataset is small and does not change frequently, a full-fledged database like PostgreSQL, MySQL, or MongoDB would be unnecessary overhead. SQLite provides a simple and efficient way to store and query the data without requiring a separate database server.

**Additional Information**  
- Lightweight and file-based – No need for a separate database server.  
- Simple deployment – No extra configuration needed for a small dataset.  
- Good for prototyping – Easy to migrate if scaling is needed. 
- Native support in Python – SQLite is built into Python’s standard library (`sqlite3` module), allowing seamless integration with the FastAPI backend without requiring external dependencies.

### 3. Frontend: Vite + React

**Decision:** The frontend is built with Vite and React.

**Why Vite + React?**
- Familiarity - I am familiar with React.
- Fast development – Vite's hot module replacement (HMR) speeds up development.  
- Optimized builds – Smaller and faster builds compared to Create React App.  
- Component-based UI – React makes it easier to build reusable UI components. 
- Support and Huge Library - React is a widely popular, making it easy to find support or libraries for features.

**Additional Choices:**  
- React-Leaflet – Used for interactive maps.  

### 4. Hosting: Railway

**Why Railway?**  
- Simple deployment – One-click deploy.
- Managed hosting – Handles SQLite file storage.  
- Free trial available - Free $5 usage for new members.

### 5. API Communication: Fetch API (CORS Enabled)

**Decision:** The frontend communicates with the backend using the fetch API.

**CORS Handling:**  
- `Access-Control-Allow-Origin: *` added to backend responses to allow cross-origin requests.  
- Environment variables (`.env`) used for API URLs instead of hardcoding.  

### 6. Architecture Overview
**Scraping:** Python
**Backend:** FastAPI + SQLite (REST API)  
**Frontend:** Vite + React (Map and Chat UI)  
**Hosting:** Railway (API and Frontend)
**API Communication:** Fetch API with JSON response
