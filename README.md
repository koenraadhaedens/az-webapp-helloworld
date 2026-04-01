# az-webapp-helloworld

Simple Hello World Node.js app for Azure App Service demo.

## Run locally

```bash
npm install
npm start
```

Open http://localhost:3000

## Deploy to Azure App Service

```bash
az login
az webapp up --name <your-app-name> --resource-group <your-rg> --runtime "NODE:20-lts" --sku S1
```

> **Note:** Deployment slots require **Standard (S1)** tier or higher. The Free (F1) tier does not support slots.

## Demo: Deployment Slots with GitHub Integration

This scenario creates a **dev** deployment slot connected to your GitHub repo. Every push to the configured branch is automatically deployed to the dev slot.

### 1. Create a GitHub repository

Push this app to a new GitHub repo:

```bash
git init
git add .
git commit -m "Initial commit"
gh repo create <your-github-user>/az-webapp-helloworld --public --source=. --push
```

### 2. Deploy the production slot

```bash
az group create --name <your-rg> --location westeurope
az webapp up --name <your-app-name> --resource-group <your-rg> --runtime "NODE:20-lts" --sku S1
```

### 3. Create a dev deployment slot

```bash
az webapp deployment slot create --name <your-app-name> --resource-group <your-rg> --slot dev
```

### 4. Connect the dev slot to GitHub

```bash
az webapp deployment source config --name <your-app-name> --resource-group <your-rg> --slot dev \
  --repo-url https://github.com/<your-github-user>/az-webapp-helloworld \
  --branch main \
  --manual-integration false
```

This enables continuous deployment — any push to `main` will automatically deploy to the **dev** slot.

### 5. Verify the dev slot

Your dev slot is available at:

```
https://<your-app-name>-dev.azurewebsites.net
```

### 6. Make a change and see it auto-deploy

Edit `app.js`, for example change the heading:

```js
<h1>Hello, World! 🚀 (dev)</h1>
```

Commit and push:

```bash
git add .
git commit -m "Update heading for dev"
git push
```

After a few moments, refresh the dev slot URL to see the change live.

### 7. Swap dev slot to production

Once you're happy with the dev slot, swap it into production:

```bash
az webapp deployment slot swap --name <your-app-name> --resource-group <your-rg> --slot dev --target-slot production
```

Your production site at `https://<your-app-name>.azurewebsites.net` now serves the updated code.