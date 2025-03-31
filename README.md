# McMap

McMap is a web application that visualizes all McDonald's outlets in Kuala Lumpur, Malaysia. It features an interactive map that details if an outlet is near another within 5km, and a chatbot to help users find specific outlets.

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
