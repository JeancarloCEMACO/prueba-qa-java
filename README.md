# Sistema de Descuentos - Spring Boot

Sistema REST API para gestión de órdenes con descuentos automáticos basados en reglas de negocio.

## Características

- Autenticación mediante JWT
- Gestión de órdenes con descuentos automáticos
- Validación de datos
- Base de datos H2 en memoria
- Spring Security

## Requisitos

- Java 17 o 21
- Maven 3.6+

## Instalación y Ejecución

```bash
# Compilar el proyecto
mvn clean install

# Ejecutar la aplicación
mvn spring-boot:run
```

La aplicación estará disponible en: `http://localhost:8080`

## Usuarios de Prueba

La aplicación crea automáticamente dos usuarios al iniciar:

- **Usuario Regular**: `user1` / `password123`
- **Usuario VIP**: `vipuser` / `password123`

## Endpoints

### 1. Login (POST /login)

Autenticar usuario y obtener token JWT.

**Request:**
```json
{
  "username": "vipuser",
  "password": "password123"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "username": "vipuser",
  "vip": true
}
```

### 2. Crear Orden (POST /orders)

Crear una nueva orden con descuentos aplicados automáticamente.

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "amount": 1500.00
}
```

**Response:**
```json
{
  "id": 1,
  "username": "vipuser",
  "originalAmount": 1500.00,
  "discountPercentage": 25.00,
  "finalAmount": 1125.00,
  "vipDiscount": true,
  "amountDiscount": true,
  "createdAt": "2026-01-09T10:30:00"
}
```

### 3. Consultar Ordenes (GET /orders)

Obtener listado de ordenes del usuario autenticado

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
[{
  "id": 1,
  "username": "vipuser",
  "originalAmount": 1500.00,
  "discountPercentage": 25.00,
  "finalAmount": 1125.00,
  "vipDiscount": true,
  "amountDiscount": true,
  "createdAt": "2026-01-09T10:30:00"
}]
```


### 4. Consultar Orden (GET /orders/{id})

Obtener detalles de una orden específica.

**Headers:**
```
Authorization: Bearer {token}
```

**Response:**
```json
{
  "id": 1,
  "username": "vipuser",
  "originalAmount": 1500.00,
  "discountPercentage": 25.00,
  "finalAmount": 1125.00,
  "vipDiscount": true,
  "amountDiscount": true,
  "createdAt": "2026-01-09T10:30:00"
}
```

## Reglas de Descuento

1. **Descuento VIP**: Si el cliente es VIP, recibe un descuento del 20%
2. **Descuento por Monto**: Si el monto de la orden es mayor a $1000, recibe un descuento adicional del 5%
3. Los descuentos son acumulativos

### Ejemplos de Descuentos

| Cliente | Monto Original | VIP (20%) | Monto > 1000 (5%) | Descuento Total | Monto Final |
|---------|---------------|-----------|-------------------|-----------------|-------------|
| Regular | $500          | No        | No                | 0%              | $500        |
| Regular | $1500         | No        | Sí                | 5%              | $1425       |
| VIP     | $500          | Sí        | No                | 20%             | $400        |
| VIP     | $1500         | Sí        | Sí                | 25%             | $1125       |

## Validaciones

- ✅ No se puede crear orden sin token válido (401 Unauthorized)
- ✅ No se puede crear orden con monto negativo o cero (400 Bad Request)
- ✅ Credenciales inválidas en login (401 Unauthorized)
- ✅ Orden no encontrada (404 Not Found)

## Ejemplos de Uso con cURL

### Login
```bash
curl -X POST http://localhost:8080/login \
  -H "Content-Type: application/json" \
  -d '{"username":"vipuser","password":"password123"}'
```

### Crear Orden
```bash
curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer {tu-token-aqui}" \
  -d '{"amount":1500.00}'
```

### Consultar Orden
```bash
curl -X GET http://localhost:8080/orders/1 \
  -H "Authorization: Bearer {tu-token-aqui}"
```

## Consola H2

Accede a la consola H2 para ver la base de datos:
- URL: http://localhost:8080/h2-console
- JDBC URL: `jdbc:h2:mem:discountdb`
- Username: `sa`
- Password: (dejar en blanco)

## Estructura del Proyecto

```
src/main/java/com/discounts/
├── DiscountSystemApplication.java
├── config/
│   └── DataLoader.java
├── controller/
│   ├── AuthController.java
│   └── OrderController.java
├── dto/
│   ├── ErrorResponse.java
│   ├── LoginRequest.java
│   ├── LoginResponse.java
│   ├── OrderRequest.java
│   └── OrderResponse.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   └── ResourceNotFoundException.java
├── model/
│   ├── Order.java
│   └── User.java
├── repository/
│   ├── OrderRepository.java
│   └── UserRepository.java
├── security/
│   ├── JwtAuthenticationFilter.java
│   ├── JwtUtil.java
│   └── SecurityConfig.java
└── service/
    ├── AuthService.java
    └── OrderService.java
```

## Tecnologías Utilizadas

- Spring Boot 3.2.1
- Spring Security
- Spring Data JPA
- JWT (jjwt 0.12.3)
- H2 Database
- Lombok
- Maven
- Java 17
