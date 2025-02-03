---
sidebar_position: 4
---
## `Endpoints.md` – Detailed API Documentation

**Purpose:**  
Document every API endpoint provided by your service.

**What to include for each endpoint:**

- **Endpoint URL and HTTP Method:**  
  Specify the URL and whether it’s a GET, POST, PUT, DELETE, etc.
  
- **Description:**  
  A brief explanation of what the endpoint does.
  
- **Parameters:**  
  List all required and optional parameters with their types and descriptions.
  
- **Request Payload:**  
  If applicable, provide examples of the request body (in JSON or another format).
  
- **Response Details:**  
  Document the expected responses, including HTTP status codes and sample response payloads.
  
- **Error Handling:**  
  Describe any error codes, messages, and common failure scenarios.

*Example Content:*
> "**Endpoint:** `POST /expenses`  
> **Description:** Create a new expense record.  
> **Parameters:**  
> - `userId` (String): The ID of the user.  
> - `amount` (Decimal): The expense amount.  
> **Request Example:**  
> ```json
> {
>   "userId": "12345",
>   "amount": 99.99,
>   "description": "Office supplies"
> }
> ```  
> **Response Example:**  
> ```json
> {
>   "expenseId": "abcde",
>   "status": "created"
> }
> ```  
> **Errors:**  
> - 400 Bad Request if required fields are missing."

---
