
Devops_Assignment
# MyApp

## Set Up and Run Locally

### Prerequisites
- Docker
- Docker Compose

### Steps

To complete the DevOps assignment as outlined, we'll need to follow a structured approach to implement CI/CD pipelines, enforce coding standards, and dockerize applications for Go, Next.js (TypeScript), and WordPress. Here's a step-by-step guide:

### Step 1: Set Up Version Control

1. **Create a New GitHub Repository:**
   - Go to GitHub and create a new public repository named "MyApp".
   - Clone the repository to your local machine.

   ```sh
   git clone https://github.com/username/MyApp.git
   cd MyApp
   ```

2. **Create Directories for Each Component:**

   ```sh
   mkdir go_app nextjs_app wordpress_app
   ```

### Step 2: Initialize Repositories for Each Component

1. **Initialize a Go Module:**

   ```sh
   cd go_app
   go mod init github.com/username/myapp/go_app
   touch main.go
   cd ..
   ```

2. **Initialize a Next.js (TypeScript) Application:**

   ```sh
   npx create-next-app@latest nextjs-app --typescript
   cd nextjs_app
   git init
   cd ..
   ```

3. **Set Up a WordPress Project:**

   ```sh
   cd wordpress-app
   # Initialize a simple PHP file for setup
   echo "<?php echo 'Hello, WordPress'; ?>" > index.php
   cd ..
   ```

### Step 3: Implement CI Workflows

1. **Create CI Workflow for Go:**
   - Create `.github/workflows/go.yml` with the following content:

     ```yaml
     name: Go CI

     on:
       push:
         branches: [main, feature/*]

     jobs:
       build:
         runs-on: ubuntu-latest

         steps:
           - uses: actions/checkout@v2
           - name: Set up Go
             uses: actions/setup-go@v2
             with:
               go-version: 1.17

           - name: Install dependencies
             run: go mod download

           - name: Run lint
             run: golangci-lint run ./...

           - name: Run tests
             run: go test ./...
     ```

2. **Create CI Workflow for Next.js (TypeScript):**
   - Create `.github/workflows/nextjs.yml` with the following content:

     ```yaml
     name: Next.js CI

     on:
       push:
         branches: [main, feature/*]

     jobs:
       build:
         runs-on: ubuntu-latest

         steps:
           - uses: actions/checkout@v2

           - name: Set up Node
             uses: actions/setup-node@v2
             with:
               node-version: '16'

           - name: Install dependencies
             run: npm install

           - name: Run lint
             run: npm run lint

           - name: Run tests
             run: npm test
     ```

3. **Create CI Workflow for WordPress:**
   - Create `.github/workflows/wordpress.yml` with the following content:

     ```yaml
     name: WordPress CI

     on:
       push:
         branches: [main, feature/*]

     jobs:
       build:
         runs-on: ubuntu-latest

         steps:
           - uses: actions/checkout@v2

           - name: Set up PHP
             uses: shivammathur/setup-php@v2
             with:
               php-version: '7.4'

           - name: Install dependencies
             run: composer install

           - name: Run PHP_CodeSniffer
             run: phpcs --standard=WordPress .

           - name: Run tests
             run: phpunit
     ```

### Step 4: Dockerize Applications

1. **Create Dockerfile for Go Application:**
   - Create `go-app/Dockerfile` with the following content:

     ```dockerfile
     FROM golang:1.17-alpine
     WORKDIR /app
     COPY . .
     RUN go build -o myapp
     CMD ["./myapp"]
     ```

2. **Create Dockerfile for Next.js Application:**
   - Create `nextjs-app/Dockerfile` with the following content:

     ```dockerfile
     FROM node:16-alpine
     WORKDIR /app
     COPY package*.json ./
     RUN npm install
     COPY . .
     RUN npm run build
     EXPOSE 3000
     CMD ["npm", "start"]
     ```

3. **Create Dockerfile for WordPress Application:**
   - Create `wordpress-app/Dockerfile` with the following content:

     ```dockerfile
     FROM wordpress:latest
     COPY . /var/www/html
     ```

### Step 5: Push Docker Images to Container Registry

1. **Create Docker Hub Repository for Each Component:**
   - Create repositories on Docker Hub for `go-app`, `nextjs-app`, and `wordpress-app`.

2. **Push Docker Images:**

   ```sh
   # For Go app
   cd go_app
   docker build -t username/go-app .
   docker push username/go-app

   # For Next.js app
   cd ../nextjs_app
   docker build -t username/nextjs-app .
   docker push username/nextjs-app

   # For WordPress app
   cd ../wordpress_app
   docker build -t username/wordpress-app .
   docker push username/wordpress-app
   ```

### Step 6: Enforce Coding Standards

1. **GolangCI-Lint for Go:**
   - Add `golangci-lint` configuration in `.golangci.yml`:

     ```yaml
     linters:
       enable:
         - errcheck
         - gosimple
         - govet
         - staticcheck
         - unused
         - deadcode
     run:
       timeout: 5m
     ```

2. **ESLint and Prettier for Next.js:**
   - Add `.eslintrc.json` and `.prettierrc` in the `nextjs-app` directory:

     ```json
     // .eslintrc.json
     {
       "extends": ["next/core-web-vitals", "prettier"]
     }
     ```

     ```json
     // .prettierrc
     {
       "singleQuote": true,
       "semi": false
     }
     ```

   - Update `package.json` scripts:

     ```json
     "scripts": {
       "lint": "eslint . --ext .js,.jsx,.ts,.tsx",
       "format": "prettier --write ."
     }
     ```

3. **PHPCS for WordPress:**
   - Create `phpcs.xml` in `wordpress-app`:

     ```xml
     <?xml version="1.0"?>
     <ruleset name="WordPress Standards">
       <rule ref="WordPress-Core" />
       <rule ref="WordPress-Docs" />
       <rule ref="WordPress-Extra" />
     </ruleset>
     ```

### Step 7: Docker Compose for Local Development

1. **Create `docker-compose.yml`:**

   ```yaml
   version: '3.8'
   services:
     go-app:
       image: yourusername/go-app
       ports:
         - "8080:8080"
     nextjs-app:
       image: yourusername/nextjs-app
       ports:
         - "3000:3000"
     wordpress-app:
       image: yourusername/wordpress-app
       ports:
         - "8081:80"
   ```

### Step 8: Continuous Deployment

1. **Extend CI/CD Pipelines:**
   - Add deployment steps to your CI workflows (example for Go):

     ```yaml
     - name: Deploy to Staging
       if: success()
       run: echo "Deploying to staging environment..."
     ```

### Step 9: Documentation

1. **Update README.md:**

   ```markdown
   # MyApp

   ## Set Up and Run Locally

   ### Prerequisites
   - Docker
   - Docker Compose

   ### Steps

   1. Clone the repository:
      ```sh
      git clone https://github.com/<username>/MyApp.git
      cd MyApp
      ```

   2. Run Docker Compose:
      ```sh
      docker-compose up
      ```

   ## Directory Structure

   - `go_app`: Go application
   - `nextjs_app`: Next.js (TypeScript) application
   - `wordpress_app`: WordPress application

   ## CI/CD Pipelines
   Each component has its own CI/CD pipeline defined in `.github/workflows`.

   ## Coding Standards
   - Go: GolangCI-Lint
   - Next.js: ESLint and Prettier
   - WordPress: PHP_CodeSniffer
   ```

### Submission

1. **Push changes to GitHub:**

   ```sh
   git add .
   git commit -m "Initial setup with CI/CD, Docker, and coding standards"
   git push origin main
   ```

2. **Provide the GitHub Repository Link:**
   - Ensure your repository is public.
   - Share the link to your GitHub repository.

