# Entrega N°2 — Websockets (Handlebars + Socket.io)

## Instalación

```bash
npm install
```

## Ejecución

```bash
npm start
```

El servidor levanta en `http://localhost:8080`.

## Rutas

### Vistas (Handlebars)
- `GET /home` → lista estática de todos los productos (se renderiza en el servidor).
- `GET /realtimeproducts` → lista de productos que se actualiza automáticamente vía **websockets**. Incluye un formulario para crear productos y un botón para eliminarlos.

### API REST
- `GET    /api/products`      → lista todos los productos
- `GET    /api/products/:pid` → detalle de un producto
- `POST   /api/products`      → crea un producto (body JSON: title, description, code, price, stock, category, status?, thumbnails?)
- `PUT    /api/products/:pid` → actualiza un producto
- `DELETE /api/products/:pid` → elimina un producto

## Cómo funciona la parte de websockets

1. El servidor (`src/app.js`) crea un `httpServer` con Express y lo envuelve con `socket.io` (`new Server(httpServer)`).
2. La instancia `io` se guarda con `app.set("io", io)` para poder usarla dentro de los routers HTTP.
3. **Creación/eliminación vía HTTP (recomendado en la consigna):** en `products.router.js`, luego de crear (`POST`) o eliminar (`DELETE`) un producto, se llama a:
   ```js
   const io = req.app.get("io");
   io.emit("updateProducts", productManager.getProducts());
   ```
   Esto le avisa a **todos los clientes conectados** (no solo al que hizo la petición) que la lista cambió.
4. **Alternativa vía sockets puros:** el servidor también escucha los eventos `newProduct` y `deleteProduct` directamente por socket (ver `io.on("connection", ...)` en `app.js`), por si se prefiere no pasar por HTTP.
5. En el cliente (`public/js/realTimeProducts.js`), al recibir el evento `updateProducts`, se vuelve a pintar la lista `<ul id="productList">` sin recargar la página.

## Estructura del proyecto

```
entrega2/
├── data/
│   └── products.json
├── public/
│   ├── css/styles.css
│   └── js/realTimeProducts.js
├── src/
│   ├── app.js
│   ├── managers/ProductManager.js
│   ├── routes/
│   │   ├── products.router.js
│   │   └── views.router.js
│   └── views/
│       ├── layouts/main.handlebars
│       ├── home.handlebars
│       └── realTimeProducts.handlebars
├── package.json
└── README.md
```
