# Guide: How to Push Files from VirtualBox to GitHub

## Step 1: Create a New GitHub Repository
1. Go to GitHub (github.com) and log into your account
2. Click the "New" button (green button) to create a new repository
3. Name your repository (e.g., `lab_1_linux_logging`)
4. Leave other settings as default
5. Click "Create repository"
6. Copy the HTTPS repository link (e.g., `https://github.com/yourusername/lab_1_linux_logging.git`)

## Step 2: Set Up VirtualBox Environment
1. Open your VirtualBox
2. Open terminal
3. Navigate to your user's home folder:
   ```bash
   cd ~
   ```

## Step 3: Initialize Git and Clone Repository
1. Initialize Git in your current directory:
   ```bash
   git init
   ```
2. Clone your GitHub repository:
   ```bash
   git clone https://github.com/yourusername/lab_1_linux_logging.git
   ```
3. Move into the cloned repository:
   ```bash
   cd lab_1_linux_logging
   ```

## Step 4: Create .gitignore File
1. Create a .gitignore file:
   ```bash
   nano .gitignore
   ```
2. Add system files you want Git to ignore:
   ```
   # System Files
   .DS_Store
   *.log
   *.tmp
   
   # IDE and Editor Files
   .idea/
   .vscode/
   *.swp
   
   # OS-specific
   Thumbs.db
   .directory
   
   # Build and Dependency Directories
   node_modules/
   dist/
   build/
   ```
3. Save the file (Ctrl + X, then Y, then Enter)

## Step 5: Add and Commit Your Files
1. Add all your project files to the repository folder
2. Stage all files for commit:
   ```bash
   git add .
   ```
3. Commit your changes:
   ```bash
   git commit -m "Initial commit: Adding project files"
   ```

## Step 6: Push to GitHub
1. Push your files to GitHub:
   ```bash
   git push -u origin main
   ```

## Troubleshooting Tips
- If you get a branch error, run:
  ```bash
  git branch -M main
  git push -u origin main
  ```
- If you get authentication errors, make sure to:
  1. Configure your Git credentials:
     ```bash
     git config --global user.name "Your GitHub Username"
     git config --global user.email "your.email@example.com"
     ```
  2. Use your GitHub credentials when prompted

## Verifying Your Push
1. Go to your GitHub repository in your web browser
2. Refresh the page
3. You should see your files listed in the repository

Remember to commit and push regularly as you make changes to your files!