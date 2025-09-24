# TP4 – Azure DevOps Pipelines (CI con Self-Hosted)
**Integrantes:** Belén Treachi y Bautista Juncos  
---

## Descripción
Este repositorio contiene una aplicación Fullstack mínima con:
- **Frontend:** React + Vite
- **Backend:** .NET 9 Web API + tests con xUnit

El objetivo del TP es construir un **pipeline CI en Azure DevOps** utilizando un **agente Self-Hosted** en macOS, que compile el front y el back, ejecute tests y publique artefactos.


## Ejecución local
**Frontend**
```bash
cd front
npm install
npm run dev     # corre en modo desarrollo (http://localhost:5173)
npm run build   # genera la carpeta /dist
```
**Backend**
```bash
cd back
dotnet restore
dotnet run      # levanta la API en http://localhost:5000
```


## Pipeline en Azure DevOps
El pipeline se encuentra en `azure-pipelines.yml`.
Se ejecuta automáticamente en cada push a `main`.

**Flujo**
1. **Build Frontend (React)**
    - Instala Node 22.x
    - `npm ci && npm run build`
    - Publica artefacto `front_dist`

2. **Build Backend (.NET 9)**
    - Instala .NET 9.x SDK
    - `dotnet restore`, `dotnet build`, `dotnet test`
    - `dotnet publish` en Release
    - Publica artefacto `back_publish`

**Artefactos**
- **front_dist** → carpeta de distribución del frontend
- **back_publish** → binarios compilados del backend listos para deploy

## Prerrequisitos del agente Self-Hosted
Para correr el pipeline en un agente local (macOS en este caso):
- **Sistema operativo:** macOS x64
- **Node.js:** ≥ 22.12.0 (se gestiona en pipeline con `NodeTool@0`)
- **.NET SDK:** 9.0.305+
- **Git** instalado
- **Permisos de Gatekeeper:** deshabilitar quarantine en los binarios del agente con:
```bash
xattr -dr com.apple.quarantine .
chmod +x ./config.sh ./run.sh ./bin/Agent.Listener
```
- **Conexión SSH a Azure DevOps** configurada:
    - Clave pública (`id_rsa.pub`) registrada en ADO
    - Remote URL:
    ```scss
    git@ssh.dev.azure.com:v3/belutp/TP4-Pipelines/TP4-Pipelines
    ```

## Puertos utilizados
- **Frontend (Vite dev server):** `5173`
- **Backend (ASP.NET Core Web API):** `5000` (HTTP) y `5001` (HTTPS, con certificado dev)

Asegurarse de que estos puertos no estén ocupados localmente para ejecutar la app.

## URLs relevantes
- **Frontend local:** http://localhost:5173
- **Backend local:** http://localhost:5000
- **Azure DevOps Proyecto:** https://dev.azure.com/belutp/TP4-Pipelines
- **Pipeline Runs:** disponible en *Pipelines* → *TP4-Pipelines* → *Runs*
- **Artifacts del pipeline:** pestaña *Artifacts* en cada run exitoso