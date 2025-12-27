# Web-Based Advanced Proxy Modeling Platform

**Course:** ENG 573 - Capstone Project (Fall 2025)  
**Sponsor:** SLB  
**Team Members:** Hung-Jui Chen, Guanghao Xu

Note：This repository contains the project presentation, final report, and user manual.
All feature descriptions, maintenance information, and operating instructions are documented in the user manual.
The source code and proprietary project resources are owned by SLB and are not included in this repository.

## Overview

This project is a web-based platform designed to replace computationally expensive, high-fidelity engineering models with efficient **proxy models**. The platform enables rapid design iteration in the early stages of product development.

The current proof-of-concept (POC) features a fully functional **Pressure Vessel Calculator**. It allows engineers to input vessel geometry, material properties, and loads to calculate critical outputs—such as Von Mises stress, deformation, and safety factors—in real-time (<0.1s).

---

## Key Features

### Advanced Calculation Capabilities
- **Forward Calculation:** Compute the **Safety Factor** based on geometry and loads.
- **Inverse Calculations:**
    - **Max Safe Pressure:** Determine the maximum internal or external pressure for a target safety factor.
    - **Geometry Optimization:** Calculate Minimum Safe Outer Diameter (OD) or Maximum Safe Inner Diameter (ID).
    - **Load Limits:** Calculate Maximum Safe Axial Load (Tension/Compression).
- **Real-Time Preview:** Calculations run "on-the-fly" as you type (debounced) for instant feedback.

### Visualization & Reporting
- **Iso-Safety Curves:** Dynamically generates `Pressure vs. Axial Force` failure envelopes using `matplotlib`.
- **Data Export:** Export results to **PDF** (with charts), **CSV**, or **JSON**.
- **Unit Conversion:** Seamless switching between **SI** (m, Pa), **Metric** (mm, MPa), and **Imperial** (in, psi) systems.

### User Management
- **Authentication:** Secure JWT-based Register, Login, and Password Reset flows.
- **History:** Saves calculation logs to a MySQL database.
- **Admin Dashboard:** User management interface for administrators.

---

## Technology Stack

- **Backend:** Python 3.10+, FastAPI (Async), Uvicorn
- **Database:** MySQL (via SQLAlchemy & PyMySQL)
- **Security:** OAuth2 with Password Flow (hashing via Passlib/Bcrypt, tokens via PyJWT)
- **Visualization:** Matplotlib (Server-side rendering)
- **Frontend:** HTML5, CSS3, Vanilla JavaScript

---

## Project Structure

```text
/capstone_project_slb
├── server.py               # Main FastAPI entry point & app configuration
├── db_init.py              # Database connection, Schema setup & default material seeding
├── security.py             # Auth logic (JWT creation/validation, Password hashing)
├── calculator_vessel.py    # Core Engineering Physics logic (Roark's Formulas)
├── data_shapes.py          # Pydantic models for data validation & API schemas
├── requirements.txt        # Python dependencies
├── /tests                  # Unit tests for physics engine
└── /Frontend
    ├── home.html           # Main SPA interface (Entry point)
    ├── style.css           # Styling & Themes
    └── script.js           # Client-side logic (API calls, UI state, Unit conversion)

```

---

## Local Setup & Installation

### 1. Prerequisites

* Python 3.8+
* MySQL Server (running locally or remotely)
* Git

### 2. Clone the Repository

```bash
git clone <your-repo-url>
cd capstone_project_slb

```

### 3. Virtual Environment

Create and activate a virtual environment to manage dependencies.

**Windows:**

```powershell
python -m venv .venv
.\.venv\Scripts\activate

```

**macOS/Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate

```

### 4. Install Dependencies

```bash
pip install -r requirements.txt

```

### 5. Database Configuration (.env)

Create a `.env` file in the root directory to store your database credentials and security keys.

```ini
# .env file content example
DATABASE_URL="mysql+pymysql://root:your_password@localhost:3306/capstone_db?charset=utf8mb4"
SECRET_KEY="your_super_secret_random_string"
ALGORITHM="HS256"
ACCESS_TOKEN_EXPIRE_MINUTES=1440

```

*Note: Adding `?charset=utf8mb4` to the connection string is recommended for full character support.*

**Initialization:**
Ensure you create the database schema first:

```sql
CREATE DATABASE capstone_db;

```

Then, run the initialization script to create tables and seed default materials:

```bash
python db_init.py

```

*Tip: `db_init.py` automatically checks for tables and seeds default materials (like Carbon Steel, SS304) if they don't exist.*

### 6. Configure Frontend API Endpoint

Open `Frontend/script.js` and update the `API_BASE` variable to point to your local server:

```javascript
// Frontend/script.js
// const API_BASE = "[https://slb-capstone-api...azurewebsites.net](https://slb-capstone-api...azurewebsites.net)"; // Cloud URL
const API_BASE = "[http://127.0.0.1:8000](http://127.0.0.1:8000)"; // Local URL

```

---

## Running the Application

### Start the Backend

Run the FastAPI server using Uvicorn:

```bash
uvicorn server:app --reload --host 127.0.0.1 --port 8000

```

The API will be available at `http://127.0.0.1:8000`.

### Launch the Frontend

Simply open `Frontend/home.html` in your web browser.

> **Tip:** For the best experience (and to avoid CORS issues with some browsers), serve the frontend using a simple HTTP server:
> ```bash
> cd Frontend
> python -m http.server 5500
> 
> ```
> 
> 
> Then visit `http://localhost:5500/home.html`.

---

## API Documentation

Interactive API documentation is generated automatically by FastAPI.

| Environment | Swagger UI (Interactive) | ReDoc (Static) | OpenAPI JSON (Raw Spec) |
| --- | --- | --- | --- |
| **Local** | [View Docs](http://127.0.0.1:8000/docs) | [View Docs](http://127.0.0.1:8000/redoc) | [Download JSON](http://127.0.0.1:8000/openapi.json) |
| **Production** | [View Docs](https://slb-capstone-api-huatesgzcmbpa9ef.westus-01.azurewebsites.net/docs) | [View Docs](https://slb-capstone-api-huatesgzcmbpa9ef.westus-01.azurewebsites.net/redoc) | [Download JSON](https://slb-capstone-api-huatesgzcmbpa9ef.westus-01.azurewebsites.net/openapi.json) |

*The `openapi.json` file can be imported into tools like Postman to generate client SDKs.*

### Key API Endpoints

* **Auth:**
* `POST /auth/register`: Register new user.
* `POST /auth/login`: Login (returns JWT token).
* `GET /me`: Get current user info (requires Auth header).


* **Calculators:**
* `POST /calculators/preview-safety-factor`: Run calculation without saving (for UI preview).
* `POST /calculators/safety-factor`: Run and save calculation to DB (requires Auth).
* `POST /calculators/iso-curve-plot`: Generates and returns a PNG failure envelope.


* **Data:**
* `GET /materials`: List all available materials seeded in the DB.



---

## Cloud Deployment (Azure)

The project is deployed on **Microsoft Azure App Service** and configured for **Continuous Deployment (CD)**.

* **Live Frontend:** [https://red-sea-01a27a51e.3.azurestaticapps.net](https://red-sea-01a27a51e.3.azurestaticapps.net) (Azure Static Web Apps)
* **Live Backend:** [https://slb-capstone-api-huatesgzcmbpa9ef.westus-01.azurewebsites.net](https://slb-capstone-api-huatesgzcmbpa9ef.westus-01.azurewebsites.net) (Azure App Service)
* **CI/CD Pipeline:** Pushing to the `main` branch on GitHub automatically triggers a build and deploy action.

**Maintenance:**

* **Environment Variables:** Production secrets (Database URL, Keys) are managed in the **Azure Portal > App Service > Settings > Environment Variables**.
* **Logs:** Application logs can be viewed in the Azure Portal under **Monitoring > Log Stream**.

---

## Physics & References

The calculation logic is derived from **Roark's Formulas for Stress and Strain (7th Ed.)**.

**Failure Criteria:**

* **Yielding:** Assessed using the **Von Mises** stress criterion.
$\sigma_{vm} = \frac{1}{\sqrt{2}}\sqrt{(\sigma_1-\sigma_2)^2 + (\sigma_2-\sigma_3)^2 + (\sigma_3-\sigma_1)^2}$
* **Safety Factor:** $SF = \frac{\text{Yield Strength} \times \text{Temp Correction}}{\text{Von Mises Stress}}$

**Calculated Stresses:**

1. **Hoop Stress** ($\sigma_t$)
2. **Radial Stress** ($\sigma_r$)
3. **Axial Stress** ($\sigma_z$)

---

## Troubleshooting & Developer Notes

* **Authentication:** Use the JWT returned from `/auth/login` as `Authorization: Bearer <token>` for all protected endpoints.
* **Unit System:** The codebase uses **SI units** (Pascals, Meters, Newtons) internally for all logic. The frontend handles conversion to/from Metric or Imperial before sending requests.
* **Database Connection:** If the DB connection fails:
* Confirm `DATABASE_URL` is correct in `.env`.
* Ensure the MySQL server is running.
* Verify `pymysql` is installed (`pip install pymysql`).


* **Security Errors:** If you see `Internal Server Error` related to JWT or Auth, ensure `SECRET_KEY` and `ALGORITHM` are set correctly in `.env` and restart the server.