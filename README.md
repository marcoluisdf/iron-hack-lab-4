# iron-hack-lab-4
Seguridad

## Scenario 1: Pseudo-Code for Authentication System

### Pseudo-Code Example:

```
FUNCTION authenticateUser(username, password):
  QUERY database WITH username AND password
  IF found RETURN True
  ELSE RETURN False

```

### Issues

- No se maneja un conteo de intentos fallidos, esto deja libertad a un atacante de poder realizar intentos indefinidos para vulnerar la seguridad

- Los querys parecen ser suceptibles a SQL Injection


```
FUNCTION authenticateUser(username, password):
  QUERY database WITH username AND password


  IF found 
    RETURN True
    attempts = 0
  ELSE 
    attempts += 1    
    RETURN False

```

- Se agrega manejo de conteos fallidos

- Lo ideal seria manejar el query de busqueda en una parte backend en la que se pueda usar un preparedstament que evique el sql injection


## Scenario 2: JWT Authentication Schema

``` 
DEFINE FUNCTION generateJWT(userCredentials):
  IF validateCredentials(userCredentials):
    SET tokenExpiration = currentTime + 3600 // Token expires in one hour
    RETURN encrypt(userCredentials + tokenExpiration, secretKey)
  ELSE:
    RETURN error

```

### Issues
 - No se provee un metodo para refresh token
 - No se tiene una invalidadcion del token antes de la expiracion
 - El metodo encryp no parace estar realizando el proceso de generacion de manera correcta



### Soluction
- Agregar un metodo para el refresh token
- Agregar un metodo para revocar el token antes de la expiracion
- Crear el token de manera correcta
    - incluir los headers
    - payload
    - signature


``` java
funcion validarCredenciales(usuario, contraseña):
    si usuario == "usuario_valido" y contraseña == "contraseña_valida":
        retornar verdadero
    sino:
        retornar falso

funcion crearJWT(payload, clave_secreta):
    header = {
        "alg": "HS256",
        "typ": "JWT"
    }
    payload_base64 = base64_encode(payload)
    header_base64 = base64_encode(header)
    firma = HMAC_SHA256(header_base64 + "." + payload_base64, clave_secreta)
    token = header_base64 + "." + payload_base64 + "." + firma
    retornar token

función revocarJWT(token):
    retornar "Token revocado"

función refreshJWT(token, clave_secreta):
    partes = dividir(token, ".")
    si longitud(partes) != 3:
        retornar "Token inválido"
    
    payload_decodificado = base64_decode(partes[1])
    nuevo_payload = actualizarPayload(payload_decodificado)
    nuevo_token = crearJWT(nuevo_payload, clave_secreta)
    retornar nuevo_token

funcion autenticarYCrearJWT(usuario, contraseña, payload, clave_secreta):
    si validarCredenciales(usuario, contraseña):
        retornar crearJWT(payload, clave_secreta)
    sino:
        retornar "Credenciales inválidas"

```


## Scenario 3: Secure Data Communication Plan

``` 
PLAN secureDataCommunication:
  IMPLEMENT SSL/TLS for all data in transit
  USE encrypted storage solutions for data at rest
  ENSURE all data exchanges comply with HTTPS protocols

  ```

Al parecer el plan propuesto para aseguramiento de los datos es aceptable, solo considerar los siguientes comentarios en su implementacion

-   Implementa SSL/TLS para todos los datos en tránsito:
    - Utilizar certificados SSL/TLS de confianza y renovarlos regularmente para evitar vulnerabilidades.
    - Configurar correctamente los protocolos SSL/TLS para utilizar versiones actualizadas y seguras.
  
-   Utiliza soluciones de almacenamiento encriptado para datos en reposo:
    - Aplicar cifrado a nivel de campo o a nivel de base de datos según sea necesario para proteger datos sensibles.
    - Realizar copias de seguridad encriptadas y almacenar las claves de cifrado de forma segura utilizando servicios de gestión de claves (KMS).
    - Regularmente realizar auditorías de seguridad y revisar los controles de acceso para detectar posibles vulnerabilidades.
    
- Asegurar que todos los intercambios de datos cumplan con los protocolos HTTPS:
    - Configurar correctamente los servidores web y las aplicaciones para forzar la redirección de HTTP a HTTPS.
    - Implementar políticas de seguridad de contenido, como CSP (Content Security Policy), para mitigar posibles ataques de inyección de código.
    - Utilizar certificados SSL/TLS de grado empresarial y validar la cadena de confianza para evitar ataques de certificados no válidos.
   