# deploy create-react-app with nginx
require [nodejs](./00.install-nodejs.md)
require [create-reactjs-app](./01.create-react-app.md)
require [nginx](./02.install-nginx.md)

ref. https://medium.com/@timmykko/deploying-create-react-app-with-nginx-and-ubuntu-e6fe83c5e9e7

```bash
mkdir /var/www
cd /var/www    
  # config file permission
  sudo gpasswd -a "$USER" www-data
  sudo chown -R "$USER":www-data /var/www
       find /var/www -type f -exec chmod 0660 {} \;
  sudo find /var/www -type d -exec chmod 2770 {} \;

  # draw out a simple app
  npx create-react-app my-app
  cd my-app
  npm install
  npm run build

  cd /etc/nginx/sites-available/
    sudo cp default default.backup
    
    # multi-line write with sudo ref. https://stackoverflow.com/a/17491223/248616
    sudo tee ./default <<EOF 
server {
  listen 80 default_server;
  root /var/www/my-app/build;
  server_name create-react-app.some-domain.com create-react-app2.some-domain.com;

  index index.html index.htm;
  location / {
    try_files \$uri /index.html;
    add_header Cache-Control no-cache;
  }
}
EOF

  cd -
cd -

: allow ingres firewall for HTTP if you are using instance@gcloud like me

# reload nginx server
sudo service nginx stop
sudo service nginx start

: we should see create-react-app now at create-react-app.some-domain.com create-react-app2.some-domain.com

```
