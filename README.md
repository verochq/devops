# CI/CD Strategy for CineLog-App

## Git Workflow

- La rama `main` está protegida.
- Todo desarrollo se hace en ramas `feature/xxx` o `bugfix/xxx`.
- Se requiere **Pull Request + aprobación + CI exitoso** para fusionar.
- Usamos tags semánticos para versiones: `git tag v1.0.0`.

## Automated Testing & Linting

El workflow de GitHub Actions:
- Se activa en `push` o `pull_request` a la rama `main`
- Ejecuta:
  1. `npm ci` → instala dependencias
  2. `npm run lint` → verifica estilo de código
  3. `npm run test:ci` → ejecuta pruebas unitarias (Vitest)
  4. `npm run build` → construye la app para producción

## Version Control Tagging

Para marcar una versión estable:
```bash
git tag -a v1.0.0 -m "Primera versión estable"
git push origin v1.0.0