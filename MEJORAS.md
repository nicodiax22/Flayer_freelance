# Lista de mejoras y propuestas para Flayer_freelance

Este documento recoge los cambios que ya apliqué, comprobaciones recomendadas y una lista priorizada de mejoras adicionales (con fragmentos y comandos) que podés aplicar cuando quieras. Lo subí a main para que lo leas y vayas marcando lo que querés implementar.

Commit aplicado (push al main):
- Mensaje: "improve: SEO, accessibility and caching — add meta tags, skip-link, rel noopener, cache headers, robots/sitemap, favicon"
- Commit: https://github.com/nicodiax22/Flayer_freelance/commit/9f1142ab4e4093217b9235f1ac07416d609c4c6d

Archivos modificados / añadidos (ya en main):
- index.html (meta OG/Twitter, rel noopener, skip-link, width/height en hero, favicon link)
- styles.css (estilos para skip-link)
- vercel.json (cabeceras Cache-Control para assets/styles/index)
- robots.txt (nuevo)
- sitemap.xml (nuevo, con la raíz)
- assets/favicon.svg (nuevo)

Cómo revisar rápidamente (checks iniciales)
- Abrir la URL pública: https://flayer-freelance.vercel.app/
- Verificar cabeceras (ejemplos):
  - curl -I https://flayer-freelance.vercel.app/styles.css
  - curl -I https://flayer-freelance.vercel.app/assets/hero-business-app.png
  - curl -I https://flayer-freelance.vercel.app/index.html
- Ejecutar Lighthouse en Chrome DevTools (Performance / Accessibility / SEO)

Prioridad alta (aplicar pronto)
1) Optimizar imágenes y añadir variantes WebP + srcset (mejora significativa en LCP)
   - Herramientas: cwebp (libwebp), ImageMagick, Squoosh o servicios automáticos.
   - Ejemplo (cwebp):
     ```bash
     # generar WebP 1200px
     cwebp -q 80 assets/hero-business-app.png -resize 1920 0 -o assets/hero-business-app-1920.webp
     cwebp -q 80 assets/hero-business-app.png -resize 1200 0 -o assets/hero-business-app-1200.webp
     cwebp -q 80 assets/hero-business-app.png -resize 900 0 -o assets/hero-business-app-900.webp
     cwebp -q 80 assets/hero-business-app.png -resize 600 0 -o assets/hero-business-app-600.webp
     ```
   - Ejemplo de etiqueta img con srcset:
     ```html
     <img
       class="hero-image"
       src="/assets/hero-business-app-1200.webp"
       srcset="/assets/hero-business-app-600.webp 600w, /assets/hero-business-app-900.webp 900w, /assets/hero-business-app-1200.webp 1200w, /assets/hero-business-app-1920.webp 1920w"
       sizes="(max-width: 620px) 100vw, 50vw"
       alt="Notebook y celular mostrando una app web para negocios"
       width="1920"
       height="1280"
     />
     ```

2) Preload de la fuente (Inter) para evitar FOIT/FOUT y mejorar LCP
   - Añadir en head (usar el mismo family que cargás):
     ```html
     <link rel="preload" href="https://fonts.gstatic.com/s/inter/vX/..../.woff2" as="font" type="font/woff2" crossorigin>
     <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
     ```
   - Alternativa: usar font-display: swap en self-hosted woff2.

3) Añadir un favicon.ico (forma tradicional) y mantener el SVG
   - Ya añadí assets/favicon.svg; opcional convertir a favicon.ico para compatibilidad.
   - Convertir con ImageMagick:
     ```bash
     convert assets/favicon.svg -background none -resize 64x64 favicon.ico
     ```

4) Comprobar y ajustar contraste de texto (accesibilidad)
   - Herramienta: axe DevTools o Lighthouse
   - Revisar títulos en hero sobre el fondo (asegurar ratio >= 4.5:1 para texto normal, 3:1 en grandes)

Prioridad media
1) Minificar styles.css o generar styles.min.css y cargarlo en producción
   - Herramienta: cssnano, clean-css, terser (para JS si existiera)
   - Ejemplo (npm):
     ```bash
     npm install -g clean-css-cli
     cleancss -o styles.min.css styles.css
     ```

2) Inline critical CSS (small) para mejorar render inicial
   - Extraer las reglas críticas para el hero y header e insertarlas inline en el head.

3) Lazy-loading para imágenes fuera del viewport
   - Añadir loading="lazy" a imágenes secundarias para mejorar LCP.

4) Añadir sitemap dinámico o mejor sitemap.xml con todas las rutas (si hay más páginas)

Prioridad baja / ideas de producto
1) Añadir un formulario de contacto simple (static form) que envíe a WhatsApp o usar un servicio (Formspree, Getform) para capturar leads.
2) Añadir JSON-LD (schema.org) para LocalBusiness o ProfessionalService para mejorar SEO local y rich snippets.
   - Ejemplo básico:
     ```html
     <script type="application/ld+json">
     {
       "@context": "https://schema.org",
       "@type": "ProfessionalService",
       "name": "Nicolas Digital",
       "url": "https://flayer-freelance.vercel.app/",
       "telephone": "+5491126251198",
       "description": "Soluciones digitales para comercios y profesionales.",
       "address": {
         "@type": "PostalAddress",
         "addressLocality": "Ciudad",
         "addressCountry": "AR"
       }
     }
     </script>
     ```

3) Añadir analytics ligero (Plausible, Fathom) o Google Analytics si lo preferís. Privacidad: Plausible no usa cookies por defecto.

4) Añadir soporte PWA (manifest.json, service worker) — opcional según objetivos.

5) Implementar validaciones automáticas en CI
   - HTML validator, stylelint, Prettier
   - Lighthouse CI para medir regresiones (configurable en GitHub Actions)
   - Ejemplo de action (Lighthouse CI): https://github.com/GoogleChrome/lighthouse-ci/tree/main/docs/github-action

Snippets útiles y comandos
- Convertir imágenes a WebP (repetido): cwebp o Squoosh
- Minificar CSS: cleancss
- Validar HTML: npm install -g html-validator-cli && html-validator --file=index.html

Propuesta de flujo mínimo recomendable (rápido wins)
1. Convertir hero y assets grandes a WebP y añadir srcset.
2. Añadir preload a la fuente más usada o self-hostear woff2 con font-display:swap.
3. Mantener index.html no cacheado, assets y CSS cacheados (ya aplicado en vercel.json).
4. Ejecutar Lighthouse y corregir issues de Accessibility / Best Practices.

Si querés, puedo aplicar alguno(s) de estos pasos directamente:
- [ ] Generar y subir variantes WebP y actualizar index.html (alto impacto).  — dime si querés que lo haga.
- [ ] Minificar styles.css y servir styles.min.css en producción.
- [ ] Añadir JSON-LD y formulario de contacto (mock) para capturar leads.
- [ ] Añadir Lighthouse CI workflow en .github/workflows/ para medir score en cada push.

Qué necesito de tu parte para avanzar con implementaciones automáticas
- Confirmá si querés que genere y suba las imágenes WebP (yo las crearé desde el PNG/PNG originales que están en /assets/).
- Decime si preferís que haga cambios directamente en main (como ya hice) o que abra PRs para revisión antes de merge.

Notas finales
- Ya apliqué cambios de bajo riesgo a main (meta tags, cache headers básicos, robots/sitemap, favicon SVG, accesibilidad mínima). Si querés revertir algo lo puedo hacer.
- Después de optimizar imágenes puede que Vercel tarde un despliegue (normal). Las cabeceras de caché asegurarán que los assets tengan TTL largo; si necesitás invalidar cache, podés redeployar o borrar el deployment desde el dashboard.

Si querés que comience por generar las WebP y actualizar index.html con srcset lo hago ahora — confirmame y lo empujo a main.
