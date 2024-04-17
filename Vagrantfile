Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  
  # Configuração da rede
  config.vm.network "private_network", type: "dhcp"
  
  # Sincroniza a pasta wallpaper
  config.vm.synced_folder "./wallpaper", "/home/vagrant/wallpaper", create: true
  
  # Configurações do provedor VirtualBox
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
    vb.gui = true
    vb.customize ['modifyvm', :id, '--graphicscontroller', 'vmsvga']
    vb.customize ['modifyvm', :id, '--vram', '128']
    vb.customize ["setextradata", :id, "CustomVideoMode1", "1920x1080x32"]
  end
  
  # Script de provisionamento inicial
  config.vm.provision "shell", inline: <<-SHELL
    if [ ! -f "/var/vagrant_provision" ]; then
      sudo apt-get update
      sudo apt-get install -y xfce4 xfce4-goodies lightdm lightdm-gtk-greeter chromium
      sudo systemctl enable lightdm
      sudo systemctl set-default graphical.target

      # Cria o diretório /etc/lightdm/lightdm.conf.d se ele não existir
      sudo mkdir -p /etc/lightdm/lightdm.conf.d

      # Configuração do LightDM para login automático do usuário 'vagrant'
      echo "[Seat:*]" | sudo tee /etc/lightdm/lightdm.conf.d/01_autologin.conf
      echo "autologin-user=vagrant" | sudo tee -a /etc/lightdm/lightdm.conf.d/01_autologin.conf
      echo "autologin-user-timeout=0" | sudo tee -a /etc/lightdm/lightdm.conf.d/01_autologin.conf
      echo "user-session=xfce" | sudo tee -a /etc/lightdm/lightdm.conf.d/01_autologin.conf

      # Define a senha do usuário 'vagrant'
      echo 'vagrant:vagrant' | sudo chpasswd
      
      # Marca para indicar que o provisionamento inicial foi concluído
      sudo touch /var/vagrant_provision
      
      # Reinicia a VM para aplicar configurações que necessitam de reboot
      sudo reboot
    fi
  SHELL

  # Script de provisionamento para configurações pós-reboot
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    # Configura o papel de parede apenas se o ambiente gráfico estiver carregado
    if pgrep xfce4-session; then
      DISPLAY=:0 xfconf-query -c xfce4-desktop -p /backdrop/screen0/monitor0/workspace0/last-image -s /home/vagrant/wallpaper/figura.jpeg
    fi
  SHELL
end

