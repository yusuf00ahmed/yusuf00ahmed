import random
from fastapi import FastAPI, Query
from pydantic import BaseModel
from typing import Optional


class Application(BaseModel):
    first_name: str
    last_name: str
    age: int
    degree: str
    interest: Optional[str] = None

class Decision(BaseModel):
    first_name: str
    last_name: str
    probability: float
    acceptance: bool

app = FastAPI()

@app.post("/applications", response_model=Decision)
async def create_application(id: int, application: Application):

    first_name = application.first_name
    last_name = application.last_name
    proba = random.random()
    acceptance = proba > 0.5

    decision = {
        "first_name": first_name,
        "last_name": last_name,
        "probability": proba,
        "acceptance": acceptance,
    }
    return decision
    
    
    from copy import deepcopy
from fastapi import FastAPI
from pydantic import BaseModel
from typing import List
from enum import Enum
import spacy


def load_models():
    """
    load the models from disk
    and put them in a dictionary
    Returns:
        dict: loaded models
    """
    models = {
        "en_sm": spacy.load("api/ml/models/en_sm"),
        "fr_sm": spacy.load("api/ml/models/fr_sm"),
    }
    print("models loaded from disk")
    return models


models = load_models()


class ModelLanguage(str, Enum):
    fr = "fr"
    en = "en"


class ModelSize(str, Enum):
    sm = "sm"
    md = "md"
    lg = "lg"


class UserRequestIn(BaseModel):
    text: str
    model_language: ModelLanguage = "en"
    model_size: ModelSize = "sm"


class EntityOut(BaseModel):
    start: int
    end: int
    type: str
    text: str


class EntitiesOut(BaseModel):
    entities: List[EntityOut]
    anonymized_text: str
    
    
