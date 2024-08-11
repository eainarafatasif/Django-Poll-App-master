# Django-Poll-App

Django poll app is a full featured polling app. You have to register in this app to show the polls and to vote. If you already voted you can not vote again. Only the owner of a poll can add poll , edit poll, update poll, delete poll , add choice, update choice, delete choice and end a poll. If a poll is ended it can not be voted. Ended poll only shows user the final result of the poll. There is a search option for polls. Also user can filter polls by name, publish date, and by number of voted. Pagination will work even after applying filter.

<h1>Getting Started</h1>
<p>These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.</p>

<h2>Prerequisites</h2>
<code>python== 3.5 or up and django==2.0 or up</code>

<h2>Installing</h2>

To deploy your Django project from GitHub using Nginx, MySQL, and an SSL certificate on an Ubuntu server, follow these steps:

### Prerequisites
- An Ubuntu server with root or sudo access.
- A domain name pointing to your server's IP address.
- A GitHub repository containing your Django project.
- A virtual environment set up for your project.
  
### 1. **Update and Install Dependencies**
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install python3-pip python3-dev libpq-dev nginx curl git
   sudo apt install mysql-server libmysqlclient-dev
   ```
Once all the packages are installed, you can start the Apache service and configure it to run on startup by entering the following commands:

```bash
 systemctl start Nginx
 systemctl enable Nginx

```
Now Allow the firewall
```bash
ufw allow 80
ufw allow 443

```

### 2. **Clone Your GitHub Repository**
   ```bash
   cd /var/www/html/
   sudo git clone https://github.com/unfoldadmin/django-unfold.git
   cd django-unfold
   ```

### 3. **Set Up Virtual Environment**
   ```bash
   sudo apt install python3-venv
   python3 -m venv projectenv
   source projectenv/bin/activate
   pip install django
   pip install mysqlclient
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

### 4. **Configure Django Settings**
- Update your `settings.py` to include your domain in the `ALLOWED_HOSTS`:
   ```python
   ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
   ```
- Ensure your `DATABASES` setting points to your MySQL database.

### 5. **Set Up MySQL Database**
   ```bash
   sudo apt install mysql-server
   sudo mysql_secure_installation
   ```
   - Log in to MySQL and create a database and user:
     ```bash
     sudo mysql -u root -p
     ```
     ```sql
     CREATE DATABASE django_unfold;
     CREATE USER 'youruser'@'localhost' IDENTIFIED BY 'yourpassword';
     GRANT ALL PRIVILEGES ON django_unfold.* TO 'youruser'@'localhost';
     FLUSH PRIVILEGES;
     EXIT;
     ```
   - Update your Django `settings.py` to connect to the MySQL database.

  ```bash
  DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_db_name',
        'USER': 'your_db_user',
        'PASSWORD': 'your_db_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}

 ```

### 6. **Run Migrations and Collect Static Files**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   python manage.py collectstatic
   ```

### 7. **Set Up Gunicorn**
   ```bash
   pip install gunicorn
   gunicorn --bind 0.0.0.0:8000 djangoproject.wsgi
   ```
Next, you need to create a systemd service file to manage the Django. First, create a Gunicorn socket file.
 ```bash
    nano /etc/systemd/system/gunicorn.socket
```
Add the following configuration.
  ```bash 
        [Unit]
        Description=gunicorn socket
        [Socket]
        ListenStream=/run/gunicorn.sock
        [Install]
        WantedBy=sockets.target
   ```
   - Create a system service file for Gunicorn:
     ```bash
     sudo nano /etc/systemd/system/gunicorn.service
     ```
     - Add the following:
       ```bash
       [Unit]
        Description=gunicorn daemon
        Requires=gunicorn.socket
        After=network.target
        [Service]
        User=root
        Group=www-data
        WorkingDirectory=/root/project
                ExecStart=/root/project/djangoenv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/run/gunicorn.sock djangoproject.wsgi:application
            [Install]
        WantedBy=multi-user.target
       ```
     - Enable and start the Gunicorn service:
       ```bash
       sudo systemctl start gunicorn
       sudo systemctl enable gunicorn
       systemctl start gunicorn.socket
       systemctl enable gunicorn.socket
       
       ```

### 8. **Configure Nginx**
   - Create an Nginx server block:
     ```bash
     sudo nano /etc/nginx/sites-available/django_unfold
     ```
     - Add the following:
       ```nginx
       server {
           listen 80;
           server_name yourdomain.com www.yourdomain.com;

           location = /favicon.ico { access_log off; log_not_found off; }
           location /static/ {
               root /var/www/html/django-unfold;
           }

           location / {
               include proxy_params;
               proxy_pass http://unix:/var/www/html/django-unfold/gunicorn.sock;
           }
       }
       ```
   - Enable the configuration and restart Nginx:
     ```bash
     sudo ln -s /etc/nginx/sites-available/django_unfold /etc/nginx/sites-enabled
     sudo nginx -t
     sudo systemctl restart nginx
     ```

Ensure Correct Permissions for Static and Media Files
If you have static and media directories outside of the main project directory, set their ownership and permissions similarly:

```bash
sudo chown -R www-data:www-data /var/www/html/django-unfold/
sudo chmod -R 755 /var/www/html/django-unfold/

```

### 9. **Obtain an SSL Certificate**
   - Install Certbot:
     ```bash
     sudo apt install certbot python3-certbot-nginx
     ```
   - Obtain and install the SSL certificate:
     ```bash
     sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
     ```
   - Follow the prompts to complete the SSL installation. Certbot will automatically configure your Nginx server block to use SSL.

### 10. **Testing**
   - Visit `https://yourdomain.com` to ensure everything is working correctly.

### 11. **Set Up Automatic Renewals for SSL**
   ```bash
   sudo systemctl status certbot.timer
   ```

This setup will deploy your Django project with Nginx, MySQL, and an SSL certificate on Ubuntu.


# Contributors
Contributions are welcome, and they are greatly appreciated! Every little bit helps, and credit will always be given.<br/><br/>

Please star the repo and feel free to make pull requests. <br/><br/>
<a href='https://ko-fi.com/J3J617AIN' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://az743702.vo.msecnd.net/cdn/kofi4.png?v=2' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>




1. **Go to the Google Cloud Console:**
   - Navigate to [Google Cloud Console](https://console.cloud.google.com/).
   - Sign in with your Google account.

2. **Create a new project:**
   - Click on the project dropdown menu at the top of the page.
   - Click on "New Project" and follow the prompts to create a new project.

3. **Enable the Google Identity service:**
   - In the Google Cloud Console, navigate to "APIs & Services" > "Dashboard."
   - Click on "Enable APIs and Services."
   - Search for "Google Identity" or "Google+ API" and enable it for your project.

4. **Create OAuth consent screen:**
   - In the Google Cloud Console, navigate to "APIs & Services" > "OAuth consent screen."
   - Fill in the required fields (like application name, user support email, etc.).
   - Add scopes (permissions) your application requires.
   - Save the consent screen information.

5. **Create OAuth credentials:**
   - In the Google Cloud Console, navigate to "APIs & Services" > "Credentials."
   - Click on "Create Credentials" > "OAuth client ID."
   - Select "Web application" as the application type.
   - Enter a name for your OAuth client.
   - Add authorized redirect URIs : `http://127.0.0.1:8000/complete/google-oauth2/`
   - Click "Create."

6. **Copy the client ID and client secret:**
   - Once the OAuth client is created, you'll see your client ID and client secret.
   - Copy these values and update the following variables in settings.py

        ```
        SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = 'your-client-id'
        SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = 'your-client-secret'
        ```

For detailed instructions, refer to Google's documentation on [OAuth 2.0](https://developers.google.com/identity/protocols/oauth2).
</details>

<details>
   <summary>Obtaining OAuth Client ID for Facebook</summary>
   
1. **Create a Facebook App:**
   - Go to the [Facebook Developers](https://developers.facebook.com/) website and log in.
   - Click on "My Apps" and then "Create App".
   - Enter the required information for your app (display name, contact email, etc.) and create the app.

2. **Configure Basic Settings:**
   - In your app dashboard, go to Settings > Basic.
   - Add a platform (select Website) and enter your site URL(http://127.0.0.1:8000/complete/facebook/).
   - Save your changes.

3. **Get App ID and App Secret:**
    
    - Update the following settings to your settings file, replacing `'your-facebook-client-id'` and `'your-facebook-client-secret'` with your actual LinkedIn app credentials:
     ```python
        SOCIAL_AUTH_FACEBOOK_OAUTH2_KEY = 'your-client-id'
        SOCIAL_AUTH_FACEBOOK_OAUTH2_SECRET = 'your-client-secret'
     ```


</details>
<details>
  <summary>Obtaining OAuth Client ID for LinkedIn</summary>

  ### Step 1: Create a LinkedIn App
  1. Go to the [LinkedIn Developer Portal](https://www.linkedin.com/developers/) and sign in.
  2. Click on "Create App" and fill in the required details, such as the app name, description, and logo.
  3. In the "Authorized Redirect URLs" section, add the callback URL for your Django app. This URL will be like `http://127.0.0.1:8000/complete/linkedin/`.
  4. Save the changes and note down the Client ID and Client Secret provided by LinkedIn.

  ### Step 2: Configure Django Settings
  
    1. Update the following settings to your settings file, replacing `'your-linkedin-client-id'` and `'your-linkedin-client-secret'` with your actual LinkedIn app credentials:
     ```python
     SOCIAL_AUTH_LINKEDIN_OAUTH2_KEY = 'your-client-id'
     SOCIAL_AUTH_LINKEDIN_OAUTH2_SECRET = 'your-client-secret'
     ```
</details>

<h2> To run the program in local server use the following command </h2>
<code>python manage.py runserver</code>

<p>Then go to http://127.0.0.1:8000 in your browser</p>

<h2>Project snapshot</h2>
<h3>Home page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409444-0e40a600-1b8c-11e9-9ab0-27d759db8973.jpg" width="100%"</img> 
</div>

<h3>Login Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409509-36c8a000-1b8c-11e9-845a-65b49262aa53.png" width="100%"</img> 
</div>

<h3>Registration Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409562-5cee4000-1b8c-11e9-82f6-1aa2df159528.png" width="100%"</img> 
</div>

<h3>Poll List Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409728-d423d400-1b8c-11e9-8903-4c08ba64512e.png" width="100%"</img> 
</div>

<h3>Poll Add Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409796-fe759180-1b8c-11e9-941b-c1202956cca4.png" width="100%"</img> 
</div>

<h3>Polling page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409843-1e0cba00-1b8d-11e9-9109-cceb79a6a623.png" width="100%"</img> 
</div>

<h3>Poll Result Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51409932-60ce9200-1b8d-11e9-9c83-c59ba498ca8b.png" width="100%"</img> 
</div>

<h3>Poll Edit Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51410008-92dff400-1b8d-11e9-8172-c228e4b60e28.png" width="100%"</img> 
</div>

<h3>Choice Update Delete Page</h3>
<div align="center">
    <img src="https://user-images.githubusercontent.com/19981097/51410442-dc7d0e80-1b8e-11e9-8f8e-18e6d7bb70fb.png" width="100%"</img> 
</div>

<h3>Dsahboard Page</h3>
<div align="center">
<img width="100%" alt="dashboard" src="https://github.com/devmahmud/Django-Poll-App/assets/17628879/46bd5f4d-b236-44c4-8636-2e171e2173e5"> 
</div>

<h2>Author</h2>
<blockquote>
  Mahmudul alam<br>
  Email: expelmahmud@gmail.com
</blockquote>

<div align="center">
    <h3>========Thank You !!!=========</h3>
</div>
