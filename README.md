# 🚀 FASTAPI DESDE CERO - GUÍA COMPLETA

**FastAPI desde Cero** es un sitio educativo completo diseñado para enseñar FastAPI desde los fundamentos hasta conceptos avanzados, con explicaciones claras, ejemplos prácticos y código listo para usar.

> *"FastAPI is a modern, fast (high-performance), web framework for building APIs with Python based on standard Python type hints."*

---

## 🎯 ¿Qué es este Proyecto?

Este proyecto proporciona un recurso educativo gratuito para aprender FastAPI, incluyendo:

- **Documentación completa** de cada tema
- **Ejemplos de código** listos para ejecutar
- **Ejercicios prácticos** para reforzar el aprendizaje
- **Sitio web educativo** con navegación intuitiva

---

## 📚 Contenido del Curso

### Módulo 1: Fundamentos

1. **Introducción**
   - ¿Qué es FastAPI?
   - Ventajas sobre Flask/Django
   - Casos de uso

2. **Instalación**
   - Python y pip
   - Virtual environments
   - Uvicorn y ASGI
   - Configuración del entorno

3. **Conceptos básicos**
   - Path operations
   - Request/Response
   - Pydantic models
   - Type hints

### Módulo 2: Intermedio

4. **Ejemplos prácticos**
   - Path parameters
   - Query parameters
   - Request body
   - Validación de datos

5. **Buenas prácticas**
   - Dependency injection
   - Security (OAuth2, JWT)
   - Testing con pytest
   - Documentación automática

### Módulo 3: Avanzado

6. **Casos reales**
   - Base de datos con SQLAlchemy
   - Async/await
   - WebSockets
   - Background tasks

7. **Proyecto final**
   - API completa
   - Deploy con Docker
   - CI/CD pipeline

---

## 🗂️ Estructura del Proyecto

```
Tercer-cap/
├── index.html          # Página principal
├── css/
│   └── styles.css      # Estilos del sitio
├── js/
│   └── main.js         # JavaScript del sitio
└── README.md
```

---

## 🚀 Cómo Usar este Proyecto

### Opción 1: Navegar el Sitio Web

1. Abre `index.html` en tu navegador
2. Navega por las secciones del curso
3. Haz clic en los temas para ver la documentación detallada

### Opción 2: Ejecutar los Ejemplos

1. Instala Python y FastAPI
2. Crea archivo .py
3. Ejecuta con `uvicorn main:app --reload`

### Requisitos

- Python 3.7+
- FastAPI y Uvicorn
- Editor de código (VS Code recomendado)

---

## 📝 Ejemplos Rápidos

### Hola Mundo

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hola Mundo"}

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

### Pydantic Models

```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr, Field

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float = Field(..., gt=0)
    description: str | None = None
    tags: list[str] = []

class User(BaseModel):
    username: str
    email: EmailStr
    full_name: str | None = None

@app.post("/items/")
async def create_item(item: Item):
    return item

@app.post("/users/")
async def create_user(user: User):
    return user
```

### Path y Query Parameters

```python
from fastapi import FastAPI, Query, Path

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(
    item_id: int = Path(..., title="ID del item", gt=0),
    q: str | None = Query(None, min_length=3, max_length=50),
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100)
):
    return {"item_id": item_id, "q": q, "skip": skip, "limit": limit}
```

### Authentication

```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

class TokenData(BaseModel):
    username: str | None = None

async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
    )
    # Validar token...
    return TokenData(username="usuario")

@app.get("/users/me")
async def read_users_me(current_user: TokenData = Depends(get_current_user)):
    return current_user
```

### Base de Datos con SQLAlchemy

```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from typing import List

from . import models, schemas, database, crud

models.Base.metadata.create_all(bind=database.engine)

app = FastAPI()

def get_db():
    db = database.SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/users/", response_model=schemas.User)
async def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    return crud.create_user(db=db, user=user)

@app.get("/users/", response_model=List[schemas.User])
async def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    return crud.get_users(db=db, skip=skip, limit=limit)
```

### WebSockets

```python
from fastapi import FastAPI, WebSocket
from fastapi.responses import HTMLContent

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        await websocket.send_text(f"Message text was: {data}")
```

### Background Tasks

```python
from fastapi import FastAPI, BackgroundTasks
import time

app = FastAPI()

def write_log(message: str):
    with open("log.txt", "a") as log:
        log.write(f"{message}\n")
    time.sleep(1)

@app.post("/send-notification/")
async def send_notification(
    email: str,
    background_tasks: BackgroundTasks
):
    background_tasks.add_task(write_log, f"Email sent to {email}")
    return {"message": f"Notification sent to {email}"}
```

---

## 🎓 Metodología de Aprendizaje

### 1. Leer la Teoría
Cada tema comienza con una explicación clara del concepto.

### 2. Ver Ejemplos
Los ejemplos de código muestran la aplicación práctica.

### 3. Practicar
Los ejercicios te permiten aplicar lo aprendido.

### 4. Experimentar
Modifica los ejemplos para entender cómo funcionan.

---

## 🔧 Comandos Esenciales

### Terminal/Consola

```bash
# Instalar FastAPI
pip install fastapi

# Instalar Uvicorn
pip install "uvicorn[standard]"

# Ejecutar servidor
uvicorn main:app --reload

# Ejecutar en producción
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4

# Instalar dependencias adicionales
pip install python-jose[cryptography]
pip install passlib[bcrypt]
pip install sqlalchemy
pip install pytest
```

---

## 📖 Recursos Adicionales

### Documentación Oficial

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [Uvicorn Documentation](https://www.uvicorn.org/)

### Herramientas Recomendadas

- **Swagger UI** - Documentación automática (/docs)
- **ReDoc** - Documentación alternativa (/redoc)
- **pytest** - Testing framework
- **httpx** - Cliente HTTP para tests

### Comunidades

- [FastAPI GitHub](https://github.com/tiangolo/fastapi)
- [Stack Overflow - FastAPI](https://stackoverflow.com/questions/tagged/fastapi)
- [Discord FastAPI](https://discord.gg/VQjSZaeJdw)

---

## 💡 Consejos para Principiantes

1. **Usa type hints**: FastAPI los usa para validación.
2. **Explora /docs**: La documentación automática es increíble.
3. **Aprende Pydantic**: Es fundamental para FastAPI.
4. **Usa async/await**: Aprovecha el rendimiento.
5. **Escribe tests**: FastAPI hace testing fácil.

---

## ⚠️ Mejores Prácticas

### Seguridad

- Usa HTTPS en producción
- Valida todos los inputs
- Implementa rate limiting

### Rendimiento

- Usa async para I/O operations
- Implementa caching
- Usa connection pooling para DB

### Código Limpio

- Separa en módulos (routers, schemas, crud)
- Usa dependency injection
- Sigue PEP 8

---

## 🧪 Ejercicios Prácticos

### Nivel Básico

1. API de tareas (TODO)
2. API de usuarios simple
3. Sistema de autenticación básico

### Nivel Intermedio

1. API con base de datos
2. Sistema de archivos subidos
3. API con paginación y filtros

### Nivel Avanzado

1. Microservicio completo
2. API con WebSockets en tiempo real
3. Sistema con celery para tareas

---

## 👨‍💻 Desarrollado por Isaac Esteban Haro Torres

**Ingeniero en Sistemas · Full Stack · Automatización · Data**

- 📧 Email: zackharo1@gmail.com
- 📱 WhatsApp: 098805517
- 💻 GitHub: https://github.com/ieharo1
- 🌐 Portafolio: https://ieharo1.github.io/portafolio-isaac.haro/

---

© 2026 Isaac Esteban Haro Torres - Todos los derechos reservados.
