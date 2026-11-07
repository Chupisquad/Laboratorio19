# Laboratorio 19 — Modernización de una Aplicación Monolítica

Patrón **Strangler Fig**: checkout monolítico, extracción del inventario y una fachada que permite alternar entre ambos flujos.

## Estructura

```text
monolith/          # Checkout y pago (puerto 4000)
inventory-service/ # Chequeo de inventario (puerto 4001)
facade/            # Enrutamiento de checkout (puerto 3000)
```

Cada servicio tiene su propio `package.json`; instala sus dependencias desde su carpeta con `npm install`.

## Partes A–C — Preparar y probar los servicios

Abre tres terminales desde la raíz del repositorio:

```powershell
cd monolith
npm install
node app.js
```

```powershell
cd inventory-service
npm install
node app.js
```

```powershell
cd facade
npm install
$env:STRANGLE_INVENTORY = "false"
node app.js
```

Prueba los servicios desde una cuarta terminal. En PowerShell:

```powershell
curl.exe -X POST http://localhost:4000/checkout -H "Content-Type: application/json" -d '{"orderId":"M1","productId":"sku-1","amount":20}'
curl.exe "http://localhost:4001/check-stock?productId=sku-1"
curl.exe -X POST http://localhost:3000/checkout -H "Content-Type: application/json" -d '{"orderId":"T1","productId":"sku-1","amount":20}'
```

El chequeo de inventario y el pago usan resultados aleatorios, así que los códigos de respuesta pueden variar. Comprueba los campos `source` y `inStock` para identificar qué servicio respondió.

## Parte D — Comparar los dos modos

Con el monolito y el servicio de inventario en ejecución, inicia la fachada en `false` para el flujo anterior. Para cambiar a `true`, detén la fachada con `Ctrl+C`, cambia la variable y vuelve a iniciarla:

```powershell
$env:STRANGLE_INVENTORY = "true"
node app.js
```

Envía el mismo `POST /checkout` a `http://localhost:3000` en ambos modos. En el modo anterior la respuesta la produce el monolito completo; en el modo modernizado, si hay inventario, el pago lo completa el monolito mediante `/checkout-payment-only`.

El chequeo de stock y el pago son aleatorios: repite las solicitudes para capturar respuestas exitosas en ambos modos. Si falta stock, el flujo modernizado responde con `source: "microservicio-inventario (via fachada)"`.

## Entregables

- Código de `monolith/app.js`, `inventory-service/app.js` y `facade/app.js`.
- Capturas de 2–3 solicitudes en cada modo, con `source` visible.
- Una explicación breve: para revertir la extracción, establecer `STRANGLE_INVENTORY=false` y reiniciar la fachada; así el checkout vuelve al monolito sin revertir todo el despliegue.

El laboratorio continúa en el **Laboratorio 20** (Proyecto Integrador).
