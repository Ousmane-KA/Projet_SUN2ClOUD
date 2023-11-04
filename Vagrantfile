Vagrant.configure("2") do |config|
  config.vm.define "deb9" do |deb9|
    deb9.vm.box = "generic/debian9"
    deb9.vm.hostname ="SRV-GLPI-01"
    deb9.vm.network "public_network", bridge:[]
    deb9.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"  # 1 Go de RAM
        vb.cpus = 1        # 1 vCPU
    end 
    deb9.vm.provision "shell",inline: <<-SHELL
      sudo sed -i '1,20d' /etc/apt/sources.list
      sudo echo "deb http://archive.debian.org/debian/ stretch main contrib non-free" >> /etc/apt/sources.list
      sudo echo "deb http://archive.debian.org/debian/ stretch-proposed-updates main contrib non-free" >> /etc/apt/sources.list 
      sudo echo "deb http://archive.debian.org/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list
      sudo apt-get update -y
      sudo apt install apache2 php7.0 php7.0-curl php7.0-json php7.0-gmp php7.0-mbstring php7.0-gd php7.0-mcrypt libapache2-mod-php7.0 php7.0-mysql php7.0-intl php7.0-sqlite3 php7.0-xml php7.0-zip mariadb-server-10.1 -y
      sudo wget https://github.com/glpi-project/glpi/releases/download/9.3.3/glpi-9.3.3.tgz
          tar zxvf glpi-9.3.3.tgz -C /tmp
      cp -r /tmp/glpi /var/www
      chown -R www-data /var/www/glpi
      sudo mysql -e "CREATE DATABASE bdd_glpi_s2c;"
      sudo mysql -e "CREATE USER 'genesys'@'localhost' IDENTIFIED BY 'P@ssw0rd1';"
      sudo mysql -e "GRANT ALL PRIVILEGES ON bdd_glpi_s2c.* TO 'genesys'@'localhost' IDENTIFIED BY 'P@ssw0rd1';"
      sudo sed -i 's|DocumentRoot /var/www/html|DocumentRoot /var/www|' /etc/apache2/sites-available/000-default.conf
      sudo apt install php php7.0-ldap php7.0-xmlrpc php7.0-imap php-apcu php-cas -y
      sudo a2enmod rewrite
      sudo echo "<Directory /var/www/glpi>" >> /etc/apache2/sites-available/000-default.conf
      sudo echo "Options Indexes FollowSymLinks" >> /etc/apache2/sites-available/000-default.conf
      sudo echo "AllowOverride All" >> /etc/apache2/sites-available/000-default.conf
      sudo echo "Require all granted" >> /etc/apache2/sites-available/000-default.conf
      sudo echo "</Directory>" >> /etc/apache2/sites-available/000-default.conf
      systemctl restart apache2
      sudo wget https://github.com/fusioninventory/fusioninventory-for-glpi/archive/glpi9.3+1.3.tar.gz
      sudo tar -zxvf glpi9.3+1.3.tar.gz -C /var/www/glpi/plugins
      sudo chown -R www-data:www-data /var/www/glpi/plugins
      cd /var/www/glpi/plugins
      mv fusioninventory-for-glpi-glpi9.3-1.3/ fusioninventory/
      systemctl restart apache2
    SHELL
  end
  config.vm.define "deb10" do |deb10|
    deb10.vm.box = "generic/debian10"
    deb10.vm.hostname = "PC-CLI-02"
    deb10.vm.network "public_network", bridge:[]
    deb10.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"  # 1 Go de RAM
      vb.cpus = 1        # 1 vCPU
    end
    deb10.vm.provision "shell", inline: <<-SHELL
      # Mettre à jour les paquets
      sudo apt-get update
      # Installer l'agent FusionInventory
      sudo apt-get install -y fusioninventory-agent
      # Créer l'utilisateur administrateur "genesys" avec le mot de passe "Adm3Pl2!"
      sudo useradd -m -s /bin/bash genesys
      echo "genesys:Adm3Pl2!" | sudo chpasswd
      # Modifier la configuration de l'agent FusionInventory
      #echo "server = http://<adresse du serveur glpi>/glpi/plugins/fusioninventory/" >> /etc/fusioninventory/agent.cfg

      # Démarrer l'agent FusionInventory
      sudo systemctl start fusioninventory-agent
      sudo systemctl enable fusioninventory-agent
    SHELL
  end

  config.vm.define "Alma8" do |Alma8|
    Alma8.vm.box = "almalinux/8"
    Alma8.vm.hostname = "PC-CLI-01"
    Alma8.vm.network "public_network", bridge:[]
    Alma8.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"  # 1 Go de RAM
      vb.cpus = 1        # 1 vCPU
    end
    Alma8.vm.provision "shell", inline: <<-SHELL
      sudo useradd -m -s /bin/bash genesys
      sudo passwd Adm3Pl2
      sudo dnf install epel-release -y 
      sudo dnf config-manager --set-enabled powertools 
      sudo dnf install fusioninventory-agent fusioninventory-agent-task-inventory -y
      #echo "server = http://<adresse du serveur glpi>/glpi/plugins/fusioninventory/" >> /etc/fusioninventory/agent.cfg
      sudo service fusioninventory-agent start
      sudo fusioninventory-agent
      SHELL
  end
  config.vm.define "WinServ19" do |WinServ19|
    WinServ19.vm.box = "StefanScherer/windows_2019"
    WinServ19.vm.hostname = "SRV-DC-01"
    WinServ19.winrm.transport = :plaintext
    WinServ19.winrm.username = "vagrant"
    WinServ19.winrm.password = "vagrant"
    WinServ19.winrm.basic_auth_only = true
    WinServ19.winrm.retry_limit = 600
    WinServ19.winrm.retry_delay = 300
    WinServ19.vm.boot_timeout = 6000
    WinServ19.vm.provider "virtualbox" do |v|
      v.name = "SRV-DC-01"
      v.cpus = 1
      v.memory = 2048
    end  
    WinServ19.vm.network "public_network", bridge:[]
    WinServ19.vm.provision "shell", inline: <<-SHELL
    netsh advfirewall firewall set rule name="WinRM-HTTP" dir=in action=allow protocol=TCP localport=5985
    netsh advfirewall firewall set rule name="WinRM-HTTP" dir=out action=allow protocol=TCP localport=5985
    $NewPassword = ConvertTo-SecureString "bHB68T6n1Lu$" -AsPlainText -Force
    Set-LocalUser -Name Administrator -Password $NewPassword
    New-LocalUser -Name "genesys" -Password (ConvertTo-SecureString "Adm3Pl2!" -AsPlainText -Force) -FullName "Genesis User" -Description "Utilisateur de test"
    New-LocalUser -Name "Xavier Pommier" -Password (ConvertTo-SecureString "Adm3Pl2!" -AsPlainText -Force) -FullName "Xavier Pommier" -Description "Utilisateur de Glpi"
    New-LocalUser -Name "Luc Toiron" -Password (ConvertTo-SecureString "Adm3Pl2!" -AsPlainText -Force) -FullName "Luc Toiron" -Description "Utilisateur de Glpi"
    New-LocalUser -Name "Hassan Nouichi" -Password (ConvertTo-SecureString "Adm3Pl2!" -AsPlainText -Force) -FullName "Hassan Nouichi" -Description "Utilisateur de Glpi"
    New-LocalUser -Name "Thierry Trinh" -Password (ConvertTo-SecureString "Adm3Pl2!" -AsPlainText -Force) -FullName "Thierry Trinh" -Description "Utilisateur de Glpi"
    Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
    Install-ADDSForest -DomainName "s2c.lab" -DomainNetBiosName "SUN3" -SafeModeAdministratorPassword (ConvertTo-SecureString "Adm3Pl2!!@EFREI" -AsPlainText -Force)  -Force
    SHELL
    end
  end

end