Credit Approval System – Backend Internship Assignment
Overview

This project is a Backend Credit Approval System developed as part of a backend internship assignment.
The system evaluates customer creditworthiness, determines loan eligibility using predefined business rules, and manages loan records through REST APIs.

The application is built using Django REST Framework, PostgreSQL, and Celery, and is fully Dockerized for easy setup and execution.

Tech Stack

Backend Framework: Django 4, Django REST Framework

Database: PostgreSQL

Background Processing: Celery + Redis

Containerization: Docker, Docker Compose

Data Processing: Pandas (Excel ingestion)

Project Structure

credit_system/
│
├── core/ Project settings and Celery configuration
├── customers/ Customer models, services, and APIs
├── loans/ Loan models, credit logic, and APIs
├── ingestion/ Background Excel ingestion tasks
├── docker/ Dockerfile and docker-compose.yml
├── manage.py
└── requirements.txt

All business logic is intentionally placed inside services files.
Views are kept minimal and handle only request and response orchestration.

Data Models
Customer

first_name

last_name

age

phone_number (unique)

monthly_income

approved_limit

current_debt

Loan

customer (Foreign Key)

loan_amount

interest_rate

tenure (months)

monthly_installment (EMI)

emis_paid_on_time

start_date

end_date

is_active

Data Ingestion

The system ingests historical data from two Excel files:

customer_data.xlsx

loan_data.xlsx

Data ingestion is handled using Celery background workers.
The ingestion process uses pandas to read Excel files, prevents duplicate records, and runs asynchronously without blocking API requests.

Business Logic
Approved Credit Limit

approved_limit = 36 × monthly_income

The value is rounded to the nearest lakh.

Credit Score Calculation (Out of 100)

Credit score is calculated using the following components:

EMIs paid on time: 40 points

Number of past loans: 20 points

Loan activity in the current year: 20 points

Loan volume compared to approved limit: 20 points

Hard Rule:
If the total active loan amount exceeds the approved credit limit, the credit score is set to 0 and the loan is rejected.

Loan Approval Rules

Credit score greater than 50: Loan approved

Credit score between 30 and 50: Loan approved with minimum interest rate of 12%

Credit score between 10 and 30: Loan approved with minimum interest rate of 16%

Credit score 10 or below: Loan rejected

Additional conditions:

If total EMI exceeds 50% of the customer’s monthly income, the loan is rejected

Interest rate is corrected if it does not meet the required slab

EMI Calculation

The monthly EMI is calculated using the compound interest formula:

EMI = P × r × (1 + r)^n / ((1 + r)^n − 1)

Where:

P = loan amount

r = annual interest rate / (12 × 100)

n = tenure in months

API Endpoints
POST /register

Registers a new customer and calculates the approved credit limit.

POST /check-eligibility

Checks loan eligibility based on credit score and business rules.

Returns:

Approval status

Corrected interest rate (if applicable)

Monthly EMI

No loan is created in this API.

POST /create-loan

Creates a loan if the customer is eligible and updates the customer’s current debt.

GET /view-loan/<loan_id>

Returns loan details along with customer information.

GET /view-loans/<customer_id>

Returns all active loans for the specified customer.

Running the Project (Docker)
Prerequisites

Docker

Docker Compose

Start the application

docker-compose up --build

This command starts:

Django API server

PostgreSQL database

Redis

Celery worker

Design Decisions

Clear separation of concerns between views and business logic

Background workers used for long-running ingestion tasks

Deterministic and explainable credit score logic

Minimal, production-ready architecture

No unnecessary features or abstractions

Notes

No frontend is implemented as per assignment requirements

Authentication is intentionally omitted

APIs can be tested using Postman or curl

Author

Benadict Jacob
Backend Internship Assignment Submission
