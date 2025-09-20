# Como Fazer o Deploy de uma API na Nuvem na Prática

## Descrição
Este projeto demonstra como criar e fazer o deploy de uma API utilizando **FastAPI** na nuvem com **Azure App Service**, incluindo um pipeline de **CI/CD com Azure DevOps**.

## Estrutura de Pastas
```
fastapi-deploy/
├── .azure-pipelines/
│   └── azure-pipelines.yml
├── app/
│   └── main.py
├── tests/
│   └── test_api.py
├── requirements.txt
└── README.md
```

## Código da API (app/main.py)
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"mensagem": "API FastAPI em execução na nuvem!"}

@app.get("/saudacao/{nome}")
def saudacao(nome: str):
    return {"mensagem": f"Olá, {nome}!"}
```

## Testes Unitários (tests/test_api.py)
```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"mensagem": "API FastAPI em execução na nuvem!"}

def test_saudacao():
    response = client.get("/saudacao/Anderson")
    assert response.status_code == 200
    assert response.json() == {"mensagem": "Olá, Anderson!"}
```

## Requisitos (requirements.txt)
```
fastapi
uvicorn
```

## Pipeline CI/CD (azure-pipelines.yml)
```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.10'
    addToPath: true

- script: |
    python -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    pip install pytest
    pytest tests/
  displayName: 'Instalar dependências e rodar testes'

- task: AzureWebApp@1
  inputs:
    azureSubscription: '<nome-da-sua-conexao>'
    appType: 'webAppLinux'
    appName: '<nome-da-sua-api>'
    package: '$(System.DefaultWorkingDirectory)/app'
```

## Implantação
1. Crie um recurso de **App Service** no portal do Azure.
2. Conecte seu repositório ao Azure DevOps.
3. Configure a conexão de serviço no pipeline.
4. Faça push no repositório e o deploy será realizado automaticamente.

---

*Este projeto é ideal para quem deseja aprender na prática como publicar uma API na nuvem com automação e boas práticas de desenvolvimento.*
