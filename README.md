from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List
import datetime

app = FastAPI()

# מודל נתונים לדוגמה
class HealthData(BaseModel):
    user_id: str
    heart_rate: int
    steps: int
    timestamp: datetime.datetime

# רשימת אחסון זמנית בזיכרון
database: List[HealthData] = []

# נקודת בדיקה בסיסית (/)
@app.get("/")
def root():
    return {"message": "API is running"}

# נקודת קבלת נתונים (POST)
@app.post("/data")
def receive_data(data: HealthData):
    database.append(data)
    return {"message": "Data received", "data": data}

# נקודת קבלת כל הנתונים (GET)
@app.get("/data")
def get_all_data():
    return database

# קבלת נתונים של משתמש ספציפי (GET /data/{user_id})
@app.get("/data/{user_id}")
def get_user_data(user_id: str):
    user_data = [d for d in database if d.user_id == user_id]
    if not user_data:
        raise HTTPException(status_code=404, detail="User data not found")
    return user_data
