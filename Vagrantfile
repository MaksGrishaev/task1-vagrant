# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.define "vm1_db" do |vm1_db|
    vm1_db.vm.box = "ubuntu/trusty32"
    vm1_db.vm.network "private_network", ip: "10.100.100.10"
    vm1_db.vm.network "forwarded_port", guest: 3306, host: 3306
    vm1_db.vm.provision "shell", inline: <<-SHELL
	echo "mysql-server mysql-server/root_password password r00t" | sudo debconf-set-selections
	echo "mysql-server mysql-server/root_password_again password r00t" | sudo debconf-set-selections
	apt-get install -y mysql-server
	mysql -uroot -p'r00t' -e "CREATE USER 'wordpress'@'%' IDENTIFIED BY '123321'; CREATE DATABASE IF NOT EXISTS wordpress; GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'%';"
	sed -i 's/127.0.0.1/0.0.0.0/' /etc/mysql/my.cnf
	service mysql restart
    SHELL
  end

  config.vm.define "vm2_web" do |vm2_web|
    vm2_web.vm.box = "ubuntu/trusty32"
    vm2_web.vm.network "private_network", ip: "10.100.100.11"
    vm2_web.vm.network "forwarded_port", guest: 80, host: 8080
    vm2_web.vm.provision "shell", inline: <<-SHELL
	apt-get install -y apache2 php5 libapache2-mod-php5 php5-mcrypt php5-mysql php5-cli wget
	wget http://wordpress.org/latest.tar.gz 2> /dev/null
	tar xzf latest.tar.gz --strip 1 -C /var/www/html
	rm /var/www/html/index.html
	chown -R www-data:www-data /var/www/
	service apache2 restart

    SHELL
  end

end
