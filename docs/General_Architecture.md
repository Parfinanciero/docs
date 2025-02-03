---
sidebar_position: 4
---

# General Architecture

Parfinanciero is designed as a modular platform built on a microservices architecture to ensure scalability, flexibility, and resilience. This document outlines the overall technical architecture, technology stack, and key design principles that drive the system.

## System Overview

Parfinanciero is a comprehensive financial analysis platform that leverages multiple microservices, each responsible for a distinct aspect of the overall system:
- **Expense Tracking**
- **AI-Driven Projections**
- **Business Intelligence Dashboard**
- **Authentication and Registration**
- **Administrative Management**
- **Financial Goals**

Each service operates independently while communicating through well-defined RESTful APIs, ensuring seamless integration and interoperability.

## Technology Stack

- **Backend Framework:**  
  The majority of services are developed using **Spring Boot** for a robust and scalable backend.
  
- **AI Projections Cell:**  
  Developed in **Python** to leverage specialized libraries and frameworks for AI and machine learning.
  
- **Databases:**  
  - **Relational Database:** PostgreSQL, used for structured data such as user profiles, financial goals, and expense records.  
  - **NoSQL Database:** MongoDB, used for storing unstructured data including scanned invoices and AI-processed data.

- **External Providers:**  
  - **Firebase:** For OAuth 2.0 authentication with Google and Microsoft.
  - **Hetzner VPS:** Hosting and deployment infrastructure.
  - **Azure Boards:** For project management using Scrum.
  - **Gemini 1.5 flash:** Provides the AI engine for predictive models and personalized recommendations.

- **Front-End:**  
  The user interface is built with **React** and **Material-UI (MUI)** to deliver a responsive and interactive experience.

## Communication and Security

- **API Communication:**  
  All services communicate over HTTP/HTTPS using RESTful APIs with JSON as the data format.
  
- **Security Measures:**  
  - APIs are secured using JWT for both authentication and authorization.
  - Each service adheres to strict API contract guidelines documented via tools like Swagger.

## Development and Deployment Practices

- **Branch Strategy:**  
  Git Flow is employed to manage code with distinct branches for production (`main`), development (`develop`), features, hotfixes, and releases.

- **CI/CD Pipeline:**  
  GitHub Actions automates:
  - Code quality checks using linters.
  - Execution of unit tests with a minimum coverage threshold of 60%.
  - Deployment pipelines, including database migrations and service restarts on Hetzner VPS.

- **Containerization:**  
  Docker is used to ensure consistency across different environments and to facilitate scalable deployments.

## Design Principles

- **Modularity:**  
  Each microservice is developed as an independent module to allow for easier maintenance and scalability.
  
- **Inter-Service Communication:**  
  Well-defined API contracts ensure reliable communication between services.
  
- **Resilience and Fault Tolerance:**  
  The architecture is designed to handle failures gracefully, ensuring continuous operation and minimal downtime.

This general architecture serves as the backbone of Parfinanciero, ensuring that the platform is robust, secure, and scalable to meet future demands.
