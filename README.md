# Web Application for Raspberry Pi (Flask + React)

This project is a simple web application with a Flask (Python) backend and a React (JavaScript) frontend, designed to be deployable on a Raspberry Pi.

## Project Structure

```
.
├── backend/                # Flask backend application
│   ├── app.py              # Main Flask application file
│   └── requirements.txt    # Python dependencies
├── frontend/               # React frontend application
│   ├── public/
│   ├── src/                # React components and source files
│   │   └── App.js          # Main React App component
│   ├── package.json        # Node.js dependencies and scripts
│   └── ...
├── DEPLOYMENT_RASPBERRY_PI.md  # Guide for deploying on a Raspberry Pi
└── README.md               # This file
```

## Development Setup

These instructions are for setting up the development environment on your local machine. For Raspberry Pi deployment, see `DEPLOYMENT_RASPBERRY_PI.md`.

### Prerequisites
*   Python 3 and pip
*   Node.js and npm

### 1. Backend Setup (Flask)
Navigate to the `backend` directory:
```bash
cd backend
```
Create a virtual environment (optional but recommended):
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
```
Install Python dependencies:
```bash
pip install -r requirements.txt
```
Run the Flask development server (usually on port 5000):
```bash
python app.py
```
The backend API should be available at `http://localhost:5000/api/data`.

### 2. Frontend Setup (React)
Open a new terminal. Navigate to the `frontend` directory:
```bash
cd frontend
```
Install Node.js dependencies:
```bash
npm install
```
Run the React development server (usually on port 3000):
```bash
npm start
```
The frontend application will open in your browser, typically at `http://localhost:3000`. It will fetch data from the backend running on port 5000.

## API
The backend exposes a simple API:
*   `GET /api/data`: Returns a sample JSON message.

## Deployment
See `DEPLOYMENT_RASPBERRY_PI.md` for instructions on how to deploy this application to a Raspberry Pi using Gunicorn (for Flask) and Nginx (for React).
