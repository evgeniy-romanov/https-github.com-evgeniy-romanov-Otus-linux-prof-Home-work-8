# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :"hw08-systemd" => {
        :box_name => "centos/7",
        :ip_addr => '192.168.56.10'
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "256"]
          end
          
          box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh; cp ~vagrant/.ssh/auth* ~root/.ssh
			yum install -y httpd
			cp /vagrant/script/alert_add.sh /opt
			cp /vagrant/script/first.conf /etc/httpd/conf
			cp /vagrant/script/httpd-first /etc/sysconfig
			cp /vagrant/script/httpd-second /etc/sysconfig
			cp '/vagrant/script/httpd@first.service' /etc/systemd/system
			cp '/vagrant/script/httpd@second.service' /etc/systemd/system
			cp /vagrant/script/second.conf /etc/httpd/conf
			cp /vagrant/script/tail_add.sh /opt
			cp /vagrant/script/watchlog /etc/sysconfig
			cp /vagrant/script/watchlog.service /etc/systemd/system
			cp /vagrant/script/watchlog.sh /opt
			cp /vagrant/script/watchlog.timer /etc/systemd/system
			chmod +x /opt/*.sh
			(crontab -l | 2>/dev/null; echo "*/3 * * * * /opt/tail_add.sh"; echo "*/5 * * * * /opt/alert_add.sh") | crontab -
			systemctl start watchlog.timer
			systemctl start watchlog.service
			systemctl enable watchlog.timer
			systemctl start httpd@first
			systemctl start httpd@second
			yum install epel-release -y && yum install spawn-fcgi php php-cli mod_fcgid httpd -y
	                cp /vagrant/script/spawn-fcgi /etc/sysconfig/
	                cp /vagrant/script/spawn-fcgi.service /etc/systemd/system/
	                systemctl start spawn-fcgi
          SHELL

      end
  end
end

