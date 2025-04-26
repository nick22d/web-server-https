Vagrant.configure("2") do |config|
    config.vm.define "web-server" do |ws|
      ws.vm.box = "ubuntu/bionic64"
      ws.vm.hostname = "web-server"
      ws.vm.network "private_network", ip: "192.168.56.10"
  
      ws.vm.provision "shell", inline: <<-SHELL
        set -e
        apt-get update
        apt-get install -y nginx openssl
        systemctl enable nginx
  
        mkdir -p /home/vagrant/ssl
  
        echo '[req]
  distinguished_name = req_distinguished_name
  x509_extensions = v3_req
  prompt = no
  
  [req_distinguished_name]
  C = US
  ST = Test
  L = Local
  O = Dev
  CN = 192.168.56.10
  
  [v3_req]
  subjectAltName = @alt_names
  
  [alt_names]
  IP.1 = 192.168.56.10' > /home/vagrant/ssl/openssl.cnf
  
        openssl req -x509 -nodes -days 3650 \
          -newkey rsa:2048 \
          -keyout /home/vagrant/ssl/selfsigned.key \
          -out /home/vagrant/ssl/selfsigned.crt \
          -config /home/vagrant/ssl/openssl.cnf \
          -extensions v3_req
  
        mkdir -p /etc/nginx/ssl
        cp /home/vagrant/ssl/selfsigned.crt /etc/nginx/ssl/
        cp /home/vagrant/ssl/selfsigned.key /etc/nginx/ssl/
        
        cp /vagrant/nginx.conf /etc/nginx/nginx.conf
  
        systemctl restart nginx
  
        # Copy certs back to /vagrant (shared folder)
        cp /home/vagrant/ssl/selfsigned.crt /vagrant/
        cp /home/vagrant/ssl/selfsigned.key /vagrant/
      SHELL
    end
  end
  