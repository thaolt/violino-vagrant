$provision_script = <<-SCRIPT
echo Provisioning...

echo Update APT repository indexes
# sudo apt-get update

package_chk () {
  dpkg -s $1 > /dev/null 2>&1
  return $?
}

PACKAGES="git texinfo chrpath diffstat build-essential byobu zsh libncurses5-dev htop tree wget curl"
MISSING=""

for PACKAGE in $PACKAGES; do
  if ! package_chk $PACKAGE; then
     echo missing $PACKAGE
     MISSING="$MISSING $PACKAGE"
  fi
done

if ! [ -z "$MISSING" ]; then
  echo Install missing packages "$MISSING"
  sudo apt-get install -y $MISSING
fi

echo Current shell $SHELL
if ! [ "$SHELL" == "/bin/zsh" ]; then
  echo Change to ZSH shell
  sudo chsh -s /bin/zsh vagrant
fi

if ! [ -d "/home/vagrant/.oh-my-zsh" ]; then
  su - vagrant -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
  sed -i -e 's/ZSH_THEME="robbyrussell"/ZSH_THEME="dpoggi"/g' /home/vagrant/.zshrc
fi

if ! [ -d "/home/vagrant/bin" ]; then
  su - vagrant -c "mkdir -p ~/bin && curl http://commondatastorage.googleapis.com/git-repo-downloads/repo 2>/dev/null > ~/bin/repo && chmod a+x ~/bin/repo"
  sed -i 's/PATH=\\${PATH}\\:\\~\\/bin//g' /home/vagrant/.zshrc
  echo "PATH=\\${PATH}:~/bin" >> /home/vagrant/.zshrc
fi

if ! [ -d "/home/vagrant/violino-bsp" ]; then
  su - vagrant -c "mkdir -p /home/vagrant/violino-bsp"
  su - vagrant -c "cd /home/vagrant/violino-bsp && ~/bin/repo init -u https://github.com/SongPhi/violino-bsp.git -b master"
fi
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = "4"
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "90"]
  end
  config.vm.provision "bootstrap", type: "shell" do |s|
    s.inline = $provision_script
  end
end
