---
title: "Building React Apps Against .NET Using DreamFactory"
linkTitle: "Building React Apps Against .NET Using DreamFactory"
weight: 20
---

# **How to Build React Apps Quickly and Securely Against .NET Backend Using DreamFactory (2025 Guide)**

## **Why Use DreamFactory for This Scenario?**

When building modern React applications that need to interact with existing enterprise infrastructure, organizations often face several challenges:

**Common Integration Challenges:**

* **Multiple authentication mechanisms**: .NET APIs might use API keys, databases require connection credentials, and SOAP services have their own authentication schemes  
* **Inconsistent API patterns**: Different backend systems expose data in different ways (REST, SOAP, direct database access)  
* **Security complexity**: Managing CORS, rate limiting, and access controls across multiple systems  
* **Development overhead**: Writing and maintaining custom middleware or backend services to orchestrate these connections

**How DreamFactory Provides Value:**

1. **Single API Gateway**: DreamFactory sits between your React app and all backend systems, providing one consistent REST interface. Your React app only needs to know one base URL and one authentication pattern.

2. **Instant API Generation**: Instead of writing custom API endpoints for database access, DreamFactory auto-generates fully documented REST APIs for your SQL Server, Oracle, or other databases with full CRUD operations.

3. **Legacy System Modernization**: SOAP services and legacy systems are automatically wrapped as clean JSON REST APIs, eliminating the need for React to handle XML parsing or complex SOAP protocols.

4. **Unified Security Model**: One role-based access control (RBAC) system manages permissions across all services. Define once what a user or application can access, and it applies to databases, APIs, and legacy systems uniformly.

5. **Reduced Development Time**: No need to build custom middleware, authentication layers, or API aggregation services. DreamFactory handles the plumbing so developers can focus on the React UI.

6. **Built-in Features**: Automatic API documentation (Swagger/OpenAPI), request logging, rate limiting, and CORS management come out of the box.

For organizations with existing .NET infrastructure, databases, and legacy SOAP services, DreamFactory eliminates months of custom integration work and provides a production-ready API layer that's secure, documented, and maintainable.

---

**Note:** *This guide references DreamFactory official documentation (docs.dreamfactory.com and guide.dreamfactory.com). For advanced features, consult DreamFactory support.*

---

## **1\. Prerequisites**

1. **Running DreamFactory instance** (Docker, VM, cloud, etc.).  
2. Access to:  
   * Base URL(s) of existing **.NET APIs**  
   * Any **databases** or **SOAP services** you want to include.  
3. **DreamFactory admin credentials**.  
4. Node.js \+ npm/yarn on your dev machine (for React).

---

## **2\. Inventory Existing Backends**

Before building anything, make a quick list:

* **.NET APIs**  
  * Base URLs (e.g., `https://api.customer.com/orders`)  
  * Auth style (Basic, API key, OAuth2, etc.)  
  * Any OpenAPI/Swagger docs available  
* **Databases**  
  * SQL Server / Oracle / others  
* **Legacy/SOAP APIs**  
  * WSDL URLs  
  * Any special auth

You'll map each of these to one or more **DreamFactory services**.

---

## **3\. Wrap .NET APIs with DreamFactory HTTP Services**

You'll use DreamFactory's **HTTP Service connector** to proxy your .NET APIs through DreamFactory. The HTTP Service connector is used to proxy third-party HTTP APIs through DreamFactory, opening up possibilities for creating sophisticated API-driven applications and powerful workflows involving multiple APIs.

### **Step 3.1 – Create an HTTP Service**

1. Log into your DreamFactory instance.  
2. Click on the **Proxy a Web Service** from the home page  
3. Choose **HTTP Service** from the **Service Type** drop down.  
4. Assign the service details (See sample below):  
   * **Name**: `dotnet_orders` (must use alphanumeric characters only; this name becomes part of your API URI structure)  
   * **Label**: "Orders API" (human-readable label for reference)  
   * **Description**: "Proxy for .NET Orders REST API" (optional referential information)

This creates DreamFactory endpoints with the structure:

```
/api/v2/dotnet_orders/<path>
```

Where `<path>` will be appended to your base URL when making requests to the remote .NET API.

### **Step 3.2 – Configure the HTTP Service**

1. Enter the **Base URL** for your .NET API (e.g., `https://api.customer.com/orders`)

Note: When a client makes a request to your DreamFactory service, DreamFactory will append everything after `/dotnet_orders` in the service URL to the base URL before forwarding the request to the remote web service. DreamFactory acts as a proxy for the remote web service.

**Important benefit**: Web service credentials are never exposed to the client since they're configured and stored in DreamFactory.

### **Step 3.3 – Add Parameters (Optional)**

If your .NET API requires query parameters (like API keys), Click the **Advanced Options** drop down and scroll down to the **Parameters** section and click the **\+** (plus sign) on the right side:

Configure each parameter:

* **Name**: The parameter name (e.g., `api_key`)  
* **Value**: The parameter value  
* **Exclude**: Check this to prevent a parameter passed from the client from being sent to the remote service  
* **Outbound**: Check this to add the parameter before DreamFactory calls the remote service  
* **Cache Key**: Optionally cache the parameter for performance  
* **Action**: Select which HTTP verbs (GET, POST, PUT, PATCH, DELETE) this parameter applies to

**Parameter behavior:**

* **Outbound**: The parameter will be passed to the destination API  
* **Exclude**: Prevents a client-provided parameter from being forwarded to the destination

### **Step 3.4 – Add Headers for Authentication**

Headers are the preferred method for passing authorization credentials because they are encrypted when HTTPS is used (unlike parameters which can be intercepted or logged).

To add a header, click the **\+** (plus sign) on the right side of the **Headers** section:

Configure each header:

* **Name**: The header name (e.g., `Authorization`, `X-API-Key`)  
* **Value**: The header value (e.g., `Bearer {token}`, your API key)  
* **Pass From Client**: Check this to pass a header from the requesting client through to the remote service  
* **Action**: Select which HTTP verbs this header applies to

**Header options:**

* **Pass From Client**: Useful if your React clients need to pass their own custom headers through DreamFactory to the .NET service  
* **Fixed value**: If unchecked, the header value you specify will be added to all requests to the remote service

### **Step 3.5 – Save Your Service**

Click **Save** to persist your HTTP Service configuration.

### **Step 3.6 – Calling Your .NET API Through DreamFactory**

Once configured, your React app will make requests to:

```
GET  https://your-dreamfactory-domain/api/v2/dotnet_orders/{endpoint}
POST https://your-dreamfactory-domain/api/v2/dotnet_orders/{endpoint}
```

DreamFactory will forward these requests to your .NET API at:

```
GET  https://api.customer.com/orders/{endpoint}
POST https://api.customer.com/orders/{endpoint}
```

All configured parameters and headers are automatically included in the outbound request to your .NET API.

### **Step 3.7 – (Optional) Add Service Definition for Enhanced Documentation**

DreamFactory supports adding OpenAPI/Swagger definitions for HTTP Services. This provides:

* Detailed API documentation in the Live API Docs  
* Better understanding of available endpoints and parameters  
* Enhanced role-based access control at the path level

If your .NET API provides OpenAPI/Swagger documentation, you can add this definition in the **Service Definition** section of the Config tab. Consult DreamFactory support or community resources for detailed instructions on importing API definitions.

**Note**: Unlike database or file storage services, HTTP services in DreamFactory do not auto-generate Swagger documentation without a service definition, as DreamFactory cannot automatically discover the structure of remote APIs.

---

## **4\. Connect Databases Directly to DreamFactory**

For any DBs you want React to hit (avoiding extra .NET plumbing):

1. **Home Page → Click on Connect to Database**.  
2. Choose the DB type (e.g., **SQL Server**).  
3. Enter connection info (host, db, user, password, etc.).  
4. Save

DreamFactory auto-generates full REST CRUD over tables/views:

```
GET    /api/v2/sqlserver/_table/customers
POST   /api/v2/sqlserver/_table/customers
PATCH  /api/v2/sqlserver/_table/customers/123
DELETE /api/v2/sqlserver/_table/customers/123
```

**Useful Query Parameters:**

* `filter` \- Add WHERE clauses (e.g., `?filter=status='active'`)  
* `fields` \- Select specific columns (e.g., `?fields=id,name,email`)  
* `related` \- Include related table data via foreign keys

All with OpenAPI docs and RBAC guardrails.

---

## **5\. Wrap SOAP / Legacy Services as REST (Optional)**

For SOAP or older web services:

1. **Services → Create → Remote Service → SOAP Service**  
2. Provide the **WSDL URL** in the "WSDL" field  
3. DreamFactory will parse operations and generate REST endpoints that call SOAP under the hood

Result: React sees simple JSON REST; DreamFactory handles all SOAP XML ugliness.

---

## **6\. Configure Security: Roles, Apps, API Keys**

Now make the React app's access secure but simple.

### **Step 6.1 – Create a Role for the React App**

1. In the left navbar, click **Role Based Access**.  
2. Click the **\+** (purple plus button) to create a new Role.  
3. Enter:  
   * **Name**: `react_frontend`  
   * **Description**: "Role for React portal application"  
   * Check **Active**  
4. Click the **Access Overview** dropdown/arrow.  
5. Configure service access for each service the React app needs:

**For the HTTP Service (wrapped .NET API):**

* **Service**: Select `dotnet_orders` (your HTTP service name)  
* **Component**: Enter `/*` (for all paths) or specific path like `/orders/*`  
* **Access**: Select the HTTP methods needed (GET, POST, PATCH, DELETE)  
* **Requester**: Select **API**

**For Database Service:**

* **Service**: Select `sqlserver` (your database service name)  
* **Component**: Enter `_table/*` (all tables) or `_table/customers` (specific table)  
* **Access**: Select the HTTP methods needed (GET, POST, PATCH, DELETE)  
* **Requester**: Select **API**

**For SOAP Service (if added):**

* **Service**: Select your SOAP service name  
* **Component**: Enter `/*` or specific operation path  
* **Access**: Select the HTTP methods needed  
* **Requester**: Select **API**  
6. Click the **\+** sign next to **Advanced Filters** to add additional service access rows as needed.  
7. Click **Save** to create the role.

**Note**: The **Requester** field determines if the service can be accessed via API calls, server-side scripts, or both. For React applications, select **API**.

**Note**: Depending on your Role Based Access Control requirements, it might make sense to create multiple Roles across your different API services. This example only uses one Role.

### **Step 6.2 – Create an App & API Key**

1. In the left navbar, click **Apps** (or **API Keys**).  
2. Click the **\+** (purple plus button) to create a new App.  
3. Fill in:  
   * **Application Name**: `react-portal`  
   * **Description**: "React portal application"  
   * Check **Active**  
   * **App Location**: Select **No Storage Required** (for web/mobile apps)  
   * **Assign a Default Role**: Select `react_frontend` (the role you just created)  
4. Click **Save**.

DreamFactory automatically generates an **API key** for this App. Copy this key \- you'll need it in your React application.

**Important**: Each App has one API key. The role assigned to the App determines what resources can be accessed using that API key.

### **Step 6.3 – Understanding JWT Session Tokens (User Authentication)**

For user-specific authentication (not just app-level):

* **API Key** (`X-DreamFactory-Api-Key` header): Identifies which **App** is making the request and determines base permissions via the App's default role  
* **Session Token** (`X-DreamFactory-Session-Token` header): Identifies which **User** is making the request (obtained by authenticating via `/user/session` endpoint)

**How it works:**

1. User logs in via `/user/session` with email/password  
2. DreamFactory returns a JWT session token in the response (`session_token` field)  
3. Subsequent requests include **both** headers:  
   * `X-DreamFactory-Api-Key`: Your App's API key  
   * `X-DreamFactory-Session-Token`: The user's session token  
4. DreamFactory combines the user's role permissions with the App's role permissions

**Authentication patterns:**

* **Unauthenticated access**: Only `X-DreamFactory-Api-Key` → Uses App's default role  
* **Authenticated access**: Both `X-DreamFactory-Api-Key` \+ `X-DreamFactory-Session-Token` → Uses user-specific permissions

Each user can be assigned a role per App in the **Users** tab of the admin console. If no user-specific role is assigned, they inherit the App's default role.

---

## **7\. Test Everything in Live API Docs**

Before touching React, verify the API layer from DreamFactory's admin.

1. Go to **API Docs** (Live API Docs).  
2. Pick:  
   * `dotnet_orders` (wrapped .NET API)  
   * `sqlserver` (DB service)  
   * `soap_legacy` (SOAP wrapper, if you added one)  
3. Add your **API key** (and session token once you set auth up).  
4. Test a few calls.

If these work here, they'll work from React.

---

## **8\. Build the React App (Talking Only to DreamFactory)**

Now assume:

* DreamFactory is reachable at: `https://api.customer.com/api/v2`  
* App API key: `DF_APP_KEY` (never commit to repo; use environment variables for deployment)

### **Step 8.1 – Create the React App**

Example with Vite \+ React:

```shell
npm create vite@latest react-portal -- --template react
cd react-portal
npm install
```

Create a `.env.local` file (for development only, never commit):

```
VITE_DF_BASE_URL=https://api.customer.com/api/v2
VITE_DF_APP_KEY=your_app_key_here
```

**Production Note:** Use your deployment platform's environment variable system (not `.env` files) to inject these values securely.

### **Step 8.2 – Helper for Calling DreamFactory**

```
// src/dreamfactoryClient.js
const BASE_URL = import.meta.env.VITE_DF_BASE_URL;
const APP_KEY = import.meta.env.VITE_DF_APP_KEY;

let sessionToken = null; // set after login

export function setSessionToken(token) {
  sessionToken = token;
}

export async function dfRequest(path, options = {}) {
  const headers = {
    "Content-Type": "application/json",
    ...(options.headers || {}),
    "X-DreamFactory-Api-Key": APP_KEY,
  };

  if (sessionToken) {
    headers["X-DreamFactory-Session-Token"] = sessionToken;
  }

  const res = await fetch(`${BASE_URL}${path}`, {
    ...options,
    headers,
  });

  if (!res.ok) {
    const errorData = await res.json().catch(() => ({}));
    throw new Error(
      `DreamFactory error: ${res.status} - ${errorData.error?.message || 'Unknown error'}`
    );
  }

  return res.json();
}
```

### **Step 8.3 – Login Against DreamFactory**

DreamFactory has built-in auth endpoints (e.g., `/user/session`) for username/password, LDAP, OAuth, etc.

Example simple login form:

```
// src/Login.jsx
import { useState } from "react";
import { dfRequest, setSessionToken } from "./dreamfactoryClient";

export default function Login({ onLoggedIn }) {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");

  async function handleSubmit(e) {
    e.preventDefault();
    setError("");
    
    try {
      const body = { email, password };

      const data = await dfRequest("/user/session", {
        method: "POST",
        body: JSON.stringify(body),
      });

      // Response includes: session_token, session_id, id, email, etc.
      setSessionToken(data.session_token);
      onLoggedIn(data);
    } catch (err) {
      setError(err.message);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      {error && <div style={{color: 'red'}}>{error}</div>}
      <input
        value={email}
        onChange={e => setEmail(e.target.value)}
        placeholder="Email"
        type="email"
        required
      />
      <input
        type="password"
        value={password}
        onChange={e => setPassword(e.target.value)}
        placeholder="Password"
        required
      />
      <button type="submit">Log in</button>
    </form>
  );
}
```

### **Step 8.4 – Calling the Wrapped .NET API From React**

Let's say your .NET API has an endpoint `GET /orders` (exposed via the HTTP Service `dotnet_orders`):

```
// src/Orders.jsx
import { useEffect, useState } from "react";
import { dfRequest } from "./dreamfactoryClient";

export default function Orders() {
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    dfRequest("/dotnet_orders/orders")
      .then(data => {
        setOrders(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {orders.map(o => (
        <li key={o.id}>
          #{o.id} – {o.status}
        </li>
      ))}
    </ul>
  );
}
```

Similarly, for a DB-backed resource with query parameters:

```
// SQL Server table via DF DB service
// Get active customers with only id, name, email fields
dfRequest("/sqlserver/_table/customers?filter=status='active'&fields=id,name,email");
```

React never touches .NET or the DB directly; it only calls DreamFactory.

---

## **9\. Handle CORS and Dev Environment**

### **Option A – Enable CORS in DreamFactory**

**Note:** CORS configuration requires environment variable setup in your DreamFactory deployment (`.env` file with `DF_CORS_*` variables). Consult your DreamFactory administrator or deployment documentation for specific configuration steps.

For development with React running on `http://localhost:5173`, you'll need to configure:

* Allowed origins  
* Allowed methods (GET, POST, PATCH, DELETE, OPTIONS)  
* Allowed headers (X-DreamFactory-Api-Key, X-DreamFactory-Session-Token, Content-Type)

### **Option B – Front Through a Reverse Proxy (Prod)**

In production, you might:

* Put everything behind a single domain (e.g., Nginx/Ingress).  
* Route `/api/` → DreamFactory.  
* Route `/` → React static site / app.

From React's perspective, it still just talks to `/api/v2/...` (same-origin, no CORS needed).

---

## **10\. Deploy**

1. **DreamFactory**:

   * Harden security: RBAC, API key rotation, rate limiting (configured per-role/per-service), logging, IP allowlists.  
   * Ensure environment variables are properly configured for production.  
2. **.NET APIs & legacy systems**:

   * Keep them private where possible (VNet, private connections).  
   * Only DreamFactory should have direct access.  
3. **React app**:

   * Build (`npm run build`) and deploy to your web host / CDN / app service.  
   * Point it at the production DreamFactory URL via deployment platform environment variables (not `.env` files).  
   * Never include API keys or secrets in your built JavaScript bundle.

---

## **Summary**

This guide demonstrates how to:

* Use DreamFactory as a unified API gateway for .NET APIs, databases, and legacy SOAP services  
* Secure access with role-based permissions and JWT authentication  
* Build a React frontend that communicates exclusively with DreamFactory

**Next Steps:**

* Test thoroughly in DreamFactory's Live API Docs before React integration  
* Implement proper error handling and loading states in your React app  
* Configure production environment variables securely  
* Set up monitoring and logging for your API gateway

**Note**: For advanced features like detailed OpenAPI imports, custom scripts, or complex authentication flows, consult DreamFactory support.