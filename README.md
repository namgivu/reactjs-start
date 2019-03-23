# install nodejs in ubuntu
ref. https://medium.com/@dooboolab/running-react-native-app-in-ubuntu-18-04-7d1db4ac7518

## install nvm
```bash
apt update

# install c++ compiler. It may already exists, but just in case.
yes | apt install build-essential libssl-dev

# install nvm aka node version manager
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash

# reload bash
source ~/.bashrc

# aftermath check 
nvm --version
```

## install node
```bsah
# install latest node
nvm install node

# set default node version for nvm
nvm alias default node

# check version of node and npm
node -v
npm -v
```
TODO what is the relationship between node vs npm?


# create reactjs app skeleton via create-react-app
ref. https://medium.com/@dooboolab/running-react-native-app-in-ubuntu-18-04-7d1db4ac7518
```
npx create-react-app my-app
```
npx is a package runner tool that comes with npm 5.2+ ref. https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b


# deploy create-react-app with nginx
ref. https://medium.com/@timmykko/deploying-create-react-app-with-nginx-and-ubuntu-e6fe83c5e9e7

## install nginx
```
yes | sudo apt install nginx
```
more details ref. https://www.rosehosting.com/blog/how-to-install-nginx-on-ubuntu-16-04/

## 
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
   server_name create-react-app.gigacover.com create-react-app2.gigacover.com;
   index index.html index.htm;
   location / {
   }
}
EOF
  cd -
cd -

: allow ingres firewall for HTTP if you are using instance@gcloud like me

# reload nginx server
sudo service nginx stop
sudo service nginx start

: we should see create-react-app now at create-react-app.gigacover.com create-react-app2.gigacover.com

```
