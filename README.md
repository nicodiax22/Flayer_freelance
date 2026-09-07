# Landing de servicios — Nicolás Díaz

Landing estática para captar trabajo freelance: automatización de procesos,
agentes de IA, reportería automática y sistemas de gestión a medida.

## Estructura

- `index.html` — la página completa, con el formulario de contacto.
- `styles.css` — tokens de color y tipografía, y todo el layout.
- `assets/` — favicon e imagen para las tarjetas de redes sociales.
- `vercel.json` — cabeceras de caché.

No usa framework ni build: es HTML, CSS y un poco de JavaScript en línea.

## El formulario

No hay backend. Al enviar, el formulario arma un mensaje con los datos
cargados y permite elegir entre WhatsApp o correo. En ambos casos abre la
aplicación correspondiente con el mensaje listo para que la persona lo revise
antes de mandarlo. El número y el correo de destino están en `index.html`.

Si en algún momento se quiere enviar el correo directamente desde la página,
sin abrir la aplicación del visitante, hace falta un endpoint: una función
serverless en Vercel con un servicio de envío, o un servicio de formularios
externo.

## Deploy en Vercel

1. Importar el repositorio `nicodiax22/Flayer_freelance`.
2. Framework preset: `Other`.
3. Build command y output directory: vacíos.

Cada push a `main` publica automáticamente.

## Ver en local

    python -m http.server 8123

y abrir http://localhost:8123
