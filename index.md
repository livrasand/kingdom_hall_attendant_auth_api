# Sign in with Kingdom Hall Attendant API

### Endpoints

#### Regístrate como desarrollador

**URL:** `/api/register_developer`  
**Method:** `POST`  
**Content-Type:** `application/json`  

**Request body:**
```json
{
  "name": "Developer Name",
  "email": "developer@example.com"
}
```

**Response:**
```json
{
  "client_id": "generated_client_id",
  "client_secret": "generated_client_secret"
}
```

#### Registrar una aplicación

**URL:** `/api/register_application`  
**Method:** `POST`  
**Content-Type:** `application/json`  

**Request body:**
```json
{
  "developer_email": "developer@example.com",
  "name": "Application Name",
  "redirect_uri": "https://example.com/callback"
}
```

**Response:**
```json
{
  "success": true
}
```

#### Ajustar límites de tasa

**URL:** `/api/adjust_rate_limit`  
**Method:** `POST`  
**Content-Type:** `application/json`  

**Request body:**
```json
{
  "developer_email": "developer@example.com",
  "rate_limit_daily": 500,
  "rate_limit_hourly": 100
}
```

**Response:**
```json
{
  "success": true
}
```

### Uso de la autenticación

#### Iniciar sesión

Para iniciar sesión en una aplicación de terceros con Kingdom Hall Attendant, utiliza el siguiente flujo:

1. Redirige al usuario a:
    ```
    https://your-domain.com/oauth/authorize?client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URI&scope=name email&state=YOUR_STATE
    ```

2. El usuario se autentica y autoriza la aplicación.

3. El usuario es redirigido de vuelta a `YOUR_REDIRECT_URI` con un código de autorización.

4. Intercambia el código de autorización por un token de acceso:
    ```bash
    curl -X POST https://your-domain.com/oauth/token \
    -d "code=AUTHORIZATION_CODE" \
    -d "client_id=YOUR_CLIENT_ID" \
    -d "client_secret=YOUR_CLIENT_SECRET" \
    -d "redirect_uri=YOUR_REDIRECT_URI" \
    -d "grant_type=authorization_code"
    ```

5. Usa el token de acceso para acceder a los recursos protegidos.

### Ejemplo de uso en JavaScript

```javascript
fetch('https://your-domain.com/oauth/token', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  body: new URLSearchParams({
    code: 'AUTHORIZATION_CODE',
    client_id: 'YOUR_CLIENT_ID',
    client_secret: 'YOUR_CLIENT_SECRET',
    redirect_uri: 'YOUR_REDIRECT_URI',
    grant_type: 'authorization_code'
  })
})
.then(response => response.json())
.then(data => console.log(data));
```

---

# Documentación para usuarios

### Endpoints

#### POST /api/register

Registra un nuevo usuario.

**URL:** `/api/register`  
**Method:** `POST`  
**Content-Type:** `application/json`  

**Request body:**
```json
{
  "email": "usuario@example.com"
}
```

**Respuestas:**

- **201 Created:** Registro exitoso.
    ```json
    {
      "message": "Registro exitoso. Se ha enviado un enlace de confirmación a tu correo."
    }
    ```

- **400 Bad Request:** Correo y contraseña son requeridos.
    ```json
    {
      "error": "Correo y contraseña son requeridos"
    }
    ```

- **409 Conflict:** El usuario ya existe.
    ```json
    {
      "error": "El usuario ya existe"
    }
    ```

- **500 Internal Server Error:** Error del servidor.
    ```json
    {
      "error": "Error: la base de datos 'kha.db' no se encontró"
    }
    ```

#### POST /api/login

Inicia sesión un usuario registrado.

**URL:** `/api/login`  
**Method:** `POST`  
**Content-Type:** `application/json`  

**Request Body:**
```json
{
  "email": "usuario@example.com",
  "password": "tu_contraseña"
}
```

**Respuestas:**

- **200 OK:** Inicio de sesión exitoso, retorna el token JWT.
    ```json
    {
      "access_token": "jwt_token_generado"
    }
    ```

- **400 Bad Request:** Correo y contraseña son requeridos.
    ```json
    {
      "error": "Correo y contraseña son requeridos"
    }
    ```

- **401 Unauthorized:** Correo o contraseña incorrectos.
    ```json
    {
      "error": "Correo o contraseña incorrectos"
    }
    ```

### Ejemplos de uso en diferentes entornos y lenguajes de programación

#### Python (usando requests)

**Registro:**

```python
import requests

url = "http://tu_dominio/api/register"
data = {
    "email": "usuario@example.com",
    "password": "tu_contraseña"
}

response = requests.post(url, json=data)

print(response.status_code)
print(response.json())
```

**Inicio de sesión:**

```python
import requests

url = "http://tu_dominio/api/login"
data = {
    "email": "usuario@example.com",
    "password": "tu_contraseña"
}

response = requests.post(url, json=data)

print(response.status_code)
print(response.json())

if response.status_code == 200:
    token = response.json()["access_token"]
    print("JWT Token:", token)
```

#### JavaScript (usando fetch)

**Registro:**

```javascript
const url = "http://tu_dominio/api/register";
const data = {
    email: "usuario@example.com",
    password: "tu_contraseña"
};

fetch(url, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify(data)
})
.then(response => response.json())
.then(data => {
    console.log('Success:', data);
})
.catch((error) => {
    console.error('Error:', error);
});
```

**Inicio de sesión:**

```javascript
const url = "http://tu_dominio/api/login";
const data = {
    email: "usuario@example.com",
    password: "tu_contraseña"
};

fetch(url, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify(data)
})
.then(response => response.json())
.then(data => {
    console.log('Success:', data);
    if (data.access_token) {
        console.log('JWT Token:', data.access_token);
    }
})
.catch((error) => {
    console.error('Error:', error);
});
```

#### cURL

**Registro:**

```sh
curl -X POST http://tu_dominio/api/register \
-H "Content-Type: application/json" \
-d '{
    "email": "usuario@example.com",
    "password": "tu_contraseña"
}'
```

**Inicio de sesión:**

```sh
curl -X POST http://tu_dominio/api/login \
-H "Content-Type: application/json" \
-d '{
    "email": "usuario@example.com",
    "password": "tu_contraseña"
}'
```

#### Java (usando HttpURLConnection)

**Registro:**

```java
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class Main {
    public static void main(String[] args) {
        try {
            URL url = new URL("http://tu_dominio/api/register");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Content-Type", "application/json; utf-8");
            conn.setRequestProperty("Accept", "application/json");
            conn.setDoOutput(true);

            String jsonInputString = "{\"email\": \"usuario@example.com\", \"password\": \"tu_contraseña\"}";

            try(OutputStream os = conn.getOutputStream()) {
                byte[] input = jsonInputString.getBytes("utf-8");
                os.write(input, 0, input.length);           
            }

            int code = conn.getResponseCode();
            System.out.println("Response Code : " + code);

            // Handle the response (not shown for brevity)
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Inicio de sesión:**

```java
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class Main {
    public static void main(String[] args) {
        try {
            URL url = new URL("http://tu_dominio/api/login");
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Content-Type", "application/json; utf-8");
            conn.setRequestProperty("Accept", "application/json");
            conn.setDoOutput(true);

            String jsonInputString = "{\"email\": \"usuario@example.com\", \"password\": \"tu_contraseña\"}";

            try(OutputStream os = conn.getOutputStream()) {
                byte[] input = jsonInputString.getBytes("utf-8");
                os.write(input

, 0, input.length);           
            }

            int code = conn.getResponseCode();
            System.out.println("Response Code : " + code);

            // Handle the response (not shown for brevity)
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---
