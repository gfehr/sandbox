# Deploying on Raspberry Pi

This guide outlines the steps to deploy the web application on a Raspberry Pi.

## Prerequisites

Ensure your Raspberry Pi is set up with Raspberry Pi OS (or a similar Linux distribution) and has internet connectivity.

## 1. Install Dependencies

### System-wide dependencies:
Update package lists:
```bash
sudo apt update
sudo apt upgrade
```

Install Python, pip (Python package installer), Node.js, npm (Node package manager), and Nginx:
```bash
sudo apt install python3 python3-pip nodejs npm nginx -y
```

Verify installations:
```bash
python3 --version
pip3 --version
node --version
npm --version
nginx -v
```

### Backend Python Dependencies:
Navigate to the `backend` directory:
```bash
cd backend
```
Install Python packages using pip:
```bash
pip3 install -r requirements.txt
```
You will also need a production-ready WSGI server like Gunicorn:
```bash
pip3 install gunicorn
```
Navigate back to the project root:
```bash
cd ..
```

### Frontend Node.js Dependencies:
Navigate to the `frontend` directory:
```bash
cd frontend
```
Install Node.js packages using npm:
```bash
npm install
```
Navigate back to the project root:
```bash
cd ..
```

## 2. Running the Backend (Flask with Gunicorn)

The backend Flask application should be run using a production WSGI server like Gunicorn.

Navigate to the `backend` directory:
```bash
cd backend
```

Run Gunicorn (replace `app:app` if your main Flask app instance or filename is different). This command will start Gunicorn on port 5000 with 3 worker processes:
```bash
gunicorn --workers 3 --bind 0.0.0.0:5000 app:app &
```
The `&` runs the process in the background. You might want to set this up as a systemd service for auto-starting on boot.

To test, from another terminal on the Pi or a machine on the same network:
```bash
curl http://<RASPBERRY_PI_IP_ADDRESS>:5000/api/data
```

## 3. Building and Serving the Frontend (React with Nginx)

### Build the React App:
Navigate to the `frontend` directory:
```bash
cd frontend
```
Create a production build of the React app:
```bash
npm run build
```
This will create a `build` directory inside `frontend` containing the static assets.

### Configure Nginx:
We need to configure Nginx to serve the static files from `frontend/build` and proxy API requests to the Gunicorn backend.

Create a new Nginx server block configuration file. For example, `/etc/nginx/sites-available/mywebapp`:
```bash
sudo nano /etc/nginx/sites-available/mywebapp
```

Paste the following configuration into the file. **Remember to replace `<YOUR_RASPBERRY_PI_IP>` with your Raspberry Pi's actual IP address or `localhost` if accessing directly on the Pi, and adjust paths if your project is located elsewhere.**

```nginx
server {
    listen 80;
    server_name <YOUR_RASPBERRY_PI_IP>; # Or your domain name

    # Serve React App
    location / {
        root /path/to/your/project/frontend/build; # ADJUST THIS PATH
        try_files $uri /index.html;
    }

    # Proxy API requests to Flask backend
    location /api {
        proxy_pass http://localhost:5000; # Assumes Gunicorn is running on port 5000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Save the file and exit nano (Ctrl+X, then Y, then Enter).

Enable the site by creating a symbolic link:
```bash
sudo ln -s /etc/nginx/sites-available/mywebapp /etc/nginx/sites-enabled/
```

Test the Nginx configuration:
```bash
sudo nginx -t
```

If the test is successful, restart Nginx:
```bash
sudo systemctl restart nginx
```

Your application should now be accessible via `http://<YOUR_RASPBERRY_PI_IP>` in a web browser.

## 4. (Optional) Setting up as a Service (Auto-start)

For Gunicorn and potentially other parts, you might want to create systemd service files so they start automatically on boot and are managed by systemd. This is beyond this basic guide but is recommended for production setups.
