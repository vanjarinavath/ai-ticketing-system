# ai-ticketing-system
ai-ticketing-system/
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   ├── routes/
│   │   ├── services/
│   │   └── ai/
│   │
│   ├── requirements.txt
│   └── .env
│
├── frontend/
│   ├── src/
│   ├── components/
│   ├── pages/
│   └── package.json
│
├── README.md
└── docker-compose.yml (optional)

git init
git remote add origin https://github.com/vanjarinavath/ai-ticketing-system.git

cd backend
pip install fastapi uvicorn openai sqlalchemy python-dotenv

fastapi
uvicorn
openai
sqlalchemy
python-dotenv

from fastapi import FastAPI
from app.routes import ticket

app = FastAPI()

app.include_router(ticket.router)

# app/ai/analyzer.py

import openai
import json
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

def analyze_ticket(text: str):
    prompt = f"""
    Analyze this ticket and return JSON:
    {text}
    """

    response = openai.ChatCompletion.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}]
    )

    return json.loads(response.choices[0].message.content)


    # app/routes/ticket.py

from fastapi import APIRouter
from app.ai.analyzer import analyze_ticket

router = APIRouter()

@router.post("/ticket")
def create_ticket(ticket: dict):
    ai_output = analyze_ticket(ticket["description"])

    if ai_output["resolution_type"] == "AUTO":
        return {
            "status": "Resolved",
            "response": "Auto-resolved response here"
        }

    return {
        "status": "Assigned",
        "department": ai_output["department"]
    }

// pages/index.js

import { useState } from "react";
import axios from "axios";

export default function Home() {
  const [text, setText] = useState("");

  const submitTicket = async () => {
    const res = await axios.post("http://localhost:8000/ticket", {
      description: text,
    });

    console.log(res.data);
  };

  return (
    <div className="p-10">
      <textarea
        className="border p-2 w-full"
        onChange={(e) => setText(e.target.value)}
      />
      <button onClick={submitTicket} className="bg-blue-500 text-white p-2">
        Submit Ticket
      </button>
    </div>
  );
}

# AI Smart Ticketing System

## Features
- AI-based ticket classification
- Auto-resolution engine
- Smart routing (department + employee)
- Employee directory with load balancing
- Ticket lifecycle management
- Analytics dashboard

## Tech Stack
- FastAPI (Backend)
- React / Next.js (Frontend)
- OpenAI GPT (AI Layer)
- SQLite (Database)

## Setup

### Backend
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload

### Frontend
cd frontend
npm install
npm run dev

git add .
git commit -m "Initial commit - AI ticketing system"
git branch -M main
git push -u origin main
