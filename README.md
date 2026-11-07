# Laboratorio 19 — Modernización de una Aplicación Monolítica

Patrón **Strangler Fig**: monolito de checkout + extracción gradual a microservicios.

Continúa en el **Laboratorio 20** (Proyecto Integrador).

## Estructura (Parte A lista)

```
monolith/
  app.js          # Checkout + pago en un solo proceso (puerto 4000)
  package.json
```

Próximas partes del lab (aún no creadas):
- `inventory-service/` — Parte B
- `facade/` — Parte C (Strangler Facade)

## Parte A — Probar el monolito

```powershell
cd monolith
node app.js
```

En otra terminal:

```powershell
curl -X POST http://localhost:4000/checkout -H "Content-Type: application/json" -d "{\"orderId\":\"M1\",\"productId\":\"sku-1\",\"amount\":20}"
```

La respuesta debe incluir `"source":"monolito"`.
