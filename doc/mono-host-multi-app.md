```bash
mkdir -p /var/www
cd /var/www    
  # config file permission
  sudo gpasswd -a "$USER" www-data # adding current user to group www-data
  sudo chown -R "$USER":www-data /var/www # set file ownership
       find /var/www -type f -exec chmod 0660 {} \; # set file access permission
  sudo find /var/www -type d -exec chmod 2770 {} \; # set folder access permission
cd -

  # draw out a simple app 01 & 02
  function my-create-react-app() {
    npx create-react-app $1
    cd $1
      npm install
      npm run build
    cd -
  } 
  p='/var/www/myapp1'; my-create-react-app $p
  p='/var/www/myapp2'; my-create-react-app $p

  # empty the :default site
  sudo tee /etc/nginx/sites-available/default <<EOF 
EOF
    
  # setup nginx site :myapp1 
  function my-deploy-site() {
    local    app_source=$1
    local    app_domain=$2
    local app_site_name=$(echo $app_source | rev | cut -d'/' -f2 | rev)
    local app_site_file="/etc/nginx/sites-available/$app_site_name" 
    
    #TODO do we need :default_server in `listen 80 default_server;` ref. https://stackoverflow.com/questions/30973774/nginx-duplicate-default-server-error#comment68827771_30974115
    sudo tee $app_site_file<<EOF 
server {
  listen 80;
  root $app_source;
  server_name $app_domain;
  
  index index.html index.htm;
  location / {
    try_files \$uri /index.html;
    add_header Cache-Control no-cache;
  }
}
EOF

    sudo ln -f -s $app_site_file /etc/nginx/sites-enabled
    sudo systemctl restart nginx
  }

  s='/var/www/myapp1/build'; d='create-react-app1.gigacover.com'; my-deploy-site $s $d
  s='/var/www/myapp2/build'; d='create-react-app2.gigacover.com'; my-deploy-site $s $d
  

: allow ingres firewall for HTTP if you are using instance@gcloud like me

# reload nginx server
sudo service nginx stop
sudo service nginx start
: or
sudo service nginx restart


: we should see create-react-app now at create-react-app.gigacover.com create-react-app2.gigacover.com

```

```bash
function abb() {
  tee /tmp/nn <<EOF
server {
  listen 80 default_server;
  root $app_source;
  server_name $app_domain;
  
  index index.html index.htm;
  location / {
    try_files \$uri /index.html;
    add_header Cache-Control no-cache;
  }
}
EOF
}
abb

```