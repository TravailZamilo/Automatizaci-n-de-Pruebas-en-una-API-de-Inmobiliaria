
# 🚀 API Inmobiliaria

Proyecto de laboratorio que crea una API REST básica con **Node.js** y **Express**, añadiendo pruebas automatizadas y un pipeline con **GitHub Actions**. Además, incluye una pequeña interfaz visual con **EJS**.

---

## 📌 Descripción

Este proyecto busca:

- Desarrollar una API simple que entregue un listado de inmuebles.
- Validar la API mediante pruebas automatizadas con **Jest** y **Supertest**.
- Implementar un workflow en **GitHub Actions** que ejecute pruebas en **Node.js 16** y **18**.
- Optimizar el pipeline usando caché de dependencias y mensajes condicionales.
- Mostrar los datos en un frontend básico con **EJS**, para dar un toque visual.

---

## 🛠 Estructura del proyecto

```
realestate-api/
├── .github/
│   └── workflows/
│       └── api-ci.yml         # Workflow de GitHub Actions
├── node_modules/              # Dependencias instaladas
├── src/
│   ├── app.js                 # API y lógica de Express
│   └── views/
│       └── index.ejs          # Vista con EJS
├── tests/
│   └── app.test.js            # Pruebas automatizadas
├── package.json               # Configuración del proyecto
├── package-lock.json
└── README.md                  # Este archivo
```

---

## 🚦 `api-ci.yml` (workflow comentado)

```yaml
name: API CI - RealEstate

on:
  push:
    branches: [main]          # Corre al hacer push en main
  pull_request:
    branches: [main]          # Corre en los PR hacia main

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node: [16.x, 18.x]    # Se prueba en Node 16 y 18

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}

      - name: Cache npm dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Success message
        if: success()
        run: echo "✅ API funcional en Node ${{ matrix.node }}"

      - name: Failure message
        if: failure()
        run: echo "❌ Fallaron los tests en Node ${{ matrix.node }}"
```

---

## ⚠️ Problemas encontrados durante el laboratorio

Durante el desarrollo y pruebas automatizadas, se presentaron los siguientes errores que vale la pena documentar:

- **`Cannot log after tests are done`**  
  Esto ocurrió porque el servidor estaba imprimiendo en consola (`console.log`) *después* de finalizar las pruebas, lo que no es compatible con Jest.

- **`app.address is not a function`**  
  El módulo exportaba solo el `app` de Express y no un servidor activo, lo que causó que Supertest no pudiera comunicarse correctamente.

- **`Jest did not exit one second after the test run has completed`**  
  Señal de que el servidor seguía abierto tras las pruebas; faltó cerrarlo al terminar.

- **Demoras en los workflows de GitHub Actions**  
  Por estos errores, el pipeline se quedaba colgado o demoraba más de lo esperado (más de 10 minutos en algunos casos).

💡 *Nota*: estos problemas son típicos al integrar servidores con pruebas, y forman parte del aprendizaje. Las soluciones incluirían exportar el servidor, cerrar conexiones tras las pruebas o ajustar el diseño de los tests.

---

## ✅ Pasos realizados

- Se creó el proyecto con `npm init -y`.
- Se instalaron Express, Jest, Supertest y EJS.
- Se desarrolló la API con ruta `/api/inmuebles`.
- Se implementaron pruebas automatizadas para esta ruta.
- Se configuró el workflow de GitHub Actions.
- Se optimizó el pipeline con cache y matrices de Node.js.
- Se añadió un frontend básico con EJS para mostrar los datos.

---

## ❓ Reflexiones finales

**Ventajas de GitHub Actions:**  
- Permite detectar errores rápidamente tras cada cambio.
- Ahorra tiempo al correr pruebas automáticamente.
- Se integra directamente en GitHub, sin necesidad de servidores extra.

**Diferencias frente a Jenkins u otros:**  
- No requiere infraestructura propia: todo corre en la nube.
- Configuración más ligera usando YAML en el propio repo.
- Muy fácil de activar y mantener dentro del ecosistema de GitHub.

**Mejoras posibles:**  
- Agregar linters (por ejemplo, ESLint).
- Incluir un paso de build o empaquetado para producción.
- Publicar artefactos o reportes de los tests.

**Buenas prácticas de seguridad a considerar:**  
- Evitar exponer secrets en los logs.
- Usar branch protection rules.
- Limitar el uso de secrets a los workflows necesarios.

---

## 💡 Ideas futuras de automatización

- **Build:** generar el paquete para producción.
- **Deploy:** desplegar automáticamente a AWS, Vercel o similar.
- **Auditoría:** integrar análisis de dependencias (Snyk, OWASP).
