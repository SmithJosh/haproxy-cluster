# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  config.vm.define "haproxy1" do |server|
    server.vm.box = "centos/6"
    server.vm.hostname = "haproxy1"
    server.vm.network "private_network", ip: "172.28.128.5"
    server.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 2048
      v.cpus = 1
    end
    # Install and configure haproxy
    server.vm.provision "shell", inline: "yum -y install haproxy"
    server.vm.provision "file", source: "./config/haproxy.cfg", destination: "/tmp/haproxy.cfg"
    server.vm.provision "shell", inline: "cp /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg"
    server.vm.provision "shell", inline: "chkconfig --add haproxy && chkconfig haproxy on"
    server.vm.provision "shell", inline: "echo net.ipv4.ip_nonlocal_bind = 1 >> /etc/sysctl.conf && sysctl -p"
    server.vm.provision "shell", inline: "service haproxy start"

    # Install and configure keepalived
    server.vm.provision "shell", inline: "yum -y install keepalived"
    server.vm.provision "file", source: "./config/keepalived_master.conf", destination: "/tmp/keepalived.conf"
    server.vm.provision "shell", inline: "cp /tmp/keepalived.conf /etc/keepalived/keepalived.conf"
    server.vm.provision "shell", inline: "chkconfig --add keepalived && chkconfig keepalived on"
    server.vm.provision "shell", inline: "service keepalived start"
  end

  config.vm.define "haproxy2" do |server|
    server.vm.box = "centos/6"
    server.vm.hostname = "haproxy2"
    server.vm.network "private_network", ip: "172.28.128.6"
    server.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 2048
      v.cpus = 1
    end
    # Install and configure haproxy
    server.vm.provision "shell", inline: "yum -y install haproxy"
    server.vm.provision "file", source: "./config/haproxy.cfg", destination: "/tmp/haproxy.cfg"
    server.vm.provision "shell", inline: "cp /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg"
    server.vm.provision "shell", inline: "chkconfig --add haproxy && chkconfig haproxy on"
    server.vm.provision "shell", inline: "echo net.ipv4.ip_nonlocal_bind = 1 >> /etc/sysctl.conf && sysctl -p"
    server.vm.provision "shell", inline: "service haproxy start"

    # Install and configure keepalived
    server.vm.provision "shell", inline: "yum -y install keepalived"
    server.vm.provision "file", source: "./config/keepalived_backup.conf", destination: "/tmp/keepalived.conf"
    server.vm.provision "shell", inline: "cp /tmp/keepalived.conf /etc/keepalived/keepalived.conf"
    server.vm.provision "shell", inline: "chkconfig --add keepalived && chkconfig keepalived on"
    server.vm.provision "shell", inline: "service keepalived start"
  end

  config.vm.define "app1" do |server|
    server.vm.box = "centos/6"
    server.vm.hostname = "app1"
    server.vm.network "private_network", ip: "172.28.128.7"
    server.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 1024
      v.cpus = 1
    end
    # Configure network
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -s 172.28.128.5 -p tcp --destination-port 8993 -j ACCEPT"
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -s 172.28.128.6 -p tcp --destination-port 8993 -j ACCEPT"
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -p tcp --destination-port 8993 -j DROP"

    # Create demo app
    server.vm.provision "shell", inline: "mkdir /opt/app && echo 'App VM #1' > /opt/app/index.html"
    server.vm.provision "file", source: "./config/app", destination: "/tmp/app"
    server.vm.provision "shell", inline: "cp /tmp/app /etc/init.d/app && chmod 755 /etc/init.d/app"
    server.vm.provision "shell", inline: "chkconfig --add app && chkconfig app on"
    server.vm.provision "shell", inline: "service app start"
  end

  config.vm.define "app2" do |server|
    server.vm.box = "centos/6"
    server.vm.hostname = "app2"
    server.vm.network "private_network", ip: "172.28.128.8"
    server.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 1024
      v.cpus = 1
    end
    # Configure network
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -s 172.28.128.5 -p tcp --destination-port 8993 -j ACCEPT"
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -s 172.28.128.6 -p tcp --destination-port 8993 -j ACCEPT"
    server.vm.provision "shell", run: "always", inline: "iptables -A INPUT -p tcp --destination-port 8993 -j DROP"

    # Create demo app
    server.vm.provision "shell", inline: "mkdir /opt/app && echo 'App VM #2' > /opt/app/index.html"
    server.vm.provision "file", source: "./config/app", destination: "/tmp/app"
    server.vm.provision "shell", inline: "cp /tmp/app /etc/init.d/app && chmod 755 /etc/init.d/app"
    server.vm.provision "shell", inline: "chkconfig --add app && chkconfig app on"
    server.vm.provision "shell", inline: "service app start"
  end
end
