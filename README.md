# Caso Enex × Enerlink

Presentación web de la prueba técnica de Product Manager para Enerlink, por Julio Araya. Es un único archivo HTML autocontenido (`index.html`): sin build, sin dependencias, solo Google Fonts como recurso externo.

## Cómo actualizar

1. Editar `index.html`.
2. Actualizar la fecha en el marcador de versión (buscar el comentario `MARCADOR DE VERSIÓN` cerca del final del archivo).
3. Commit y push a `main`:

```bash
git add index.html
git commit -m "Descripción del cambio"
git push
```

Vercel está conectado al repo y despliega automáticamente cada push a `main`.
