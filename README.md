# CI/CD Strategy for CineLog-App

## Full Pipeline Flow

1. 📥 Developer commits code to a feature branch
2. 🔄 Opens Pull Request to `main`
3. 🧪 GitHub Actions runs:
   - `npm run lint`
   - `npm run test:ci`
   - `npm run build`
4. ✅ If all steps pass → PR can be merged to `main`
5. 🏷️ To deploy to production:
   ```bash
   git tag -a v1.0.0 -m "Release v1.0.0"
   git push origin v1.0.0