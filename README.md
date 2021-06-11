# lab10


Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием **Vagrant**

```sh
$ open https://www.vagrantup.com/intro/index.html
```
Определение: 
Vagrant-это инструмент для создания и управления средами виртуальных машин в рамках одного рабочего процесса. Благодаря простому в использовании рабочему процессу и сосредоточенности на автоматизации, Vagrant сокращает время настройки среды разработки, увеличивает четность производства и делает оправдание "работы на моей машине" пережитком прошлого.

## Tasks

- [x] 1. Ознакомиться со ссылками учебного материала
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Составить отчет и отправить ссылку личным сообщением в **Slack**



```sh
$ export GITHUB_USERNAME= пишу свое имя пользователя
$ export PACKAGE_MANAGER= пишу свой пакетный менетджер
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ ${PACKAGE_MANAGER} install vagrant ##установка vagrant из менеджера пакетов
```

```sh
$ vagrant version

Installed Version: 2.2.6

Vagrant was unable to check for the latest version of Vagrant.
Please check manually at https://www.vagrantup.com


$ vagrant init bento/ubuntu-19.10 ##инициализация виртуальной машины на ubuntu

 `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.



$ less Vagrantfile ##сгенерируется файл настроек виртуальной машины
Создаем новый Vagrantfile, перезаписываем изначальный (флаг -f), находящийся в текущем пути. Причем информация в нем будет в минимальном объеме (флаг -m)
$ vagrant init -f -m bento/ubuntu-19.10

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

```sh
$ mkdir shared
```

```sh
$ cat > Vagrantfile <<EOF ##добавление конфигурации
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```

```sh
# В файл Vagrantfile записываем конфигурацию для виртуальной машины
# vagrant-vbguest - это плагин, который автоматически обновляет гостевые дополнения VirtualBox

$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```

```sh
# Продолжаем конфигурацию для виртуальной машины:
$ cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"                              # Указываем версию виртуальной машины: ubuntu-19.10
  config.vm.network "public_network"                                # Указываем настройки сети: public_network
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')      # Указываем связующие директории: 'shared', '/vagrant', type: 'rsync'

  config.vm.provider "virtualbox" do |vb|                           # Указываем тип виртуальной машины: virtualbox
    vb.gui = true                                                   # Указываем, что используется графический интерфейс: vb.gui = true 
    vb.memory = "2048"                                              # Указываем, сколько выделяем оперативной памяти под виртуальную машину: 2048МБ 
  end

  config.vm.provision "shell", inline: \$script, privileged: true   # config.vm.provision "shell" - задает встроенную команду оболочки для выполнения на удаленном компьютере

  config.ssh.extra_args = "-tt"                                     # config.ssh.extra_args - значение настроек передается непосредственно в исполняемый файл ssh#

end
EOF
```

```sh
# проверка корректности файла Vagrantfile
$ vagrant validate ##валидация

Vagrantfile validated successfully.

$ vagrant status ##проверка статуса

$ vagrant up # --provider virtualbox ##запуск виртуальной машины
$ vagrant port ##просмотр порта
    
    22 (guest) => 2222 (host)
    80 (guest) => 8080 (host)

$ vagrant status

$ vagrant ssh ##подключение к виртуальной машине через ssh

$ vagrant snapshot list ##просмотр снимков виртуальных машин
$ vagrant snapshot push ##добавление снимка виртуальной машины
$ vagrant snapshot list
$ vagrant halt ##выключение виртуальной машины
$ vagrant snapshot pop ##открытие снимка виртуальной машины
```

```ruby
  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```

```sh
$ vagrant plugin install vagrant-vmware-esxi ##установка плагина vmware

Installing the 'vagrant-vmware-esxi' plugin. This can take a few minutes...
Fetching iniparse-1.5.0.gem
Fetching racc-1.5.2.gem
Building native extensions. This could take a while...
Fetching nokogiri-1.11.7-x86_64-linux.gem
Fetching vagrant-vmware-esxi-2.5.2.gem


$ vagrant plugin list ##просмотр плагинов

vagrant-global-status (0.1.4, global)
vagrant-vbguest (0.30.0, global)
vagrant-vmware-esxi (2.5.2, global)

$ vagrant up --provider=vmware_esxi ##запуск виртуальной машины с указанием провайдера
```

## Report

```sh
$ cd ~/workspace/
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [VirualBox](https://www.virtualbox.org/)
- [Vagrant providers](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins#providers)
- [Vagrant vbguest plugin](https://github.com/dotless-de/vagrant-vbguest)
- [Vagrant disksize plugin](https://github.com/sprotheroe/vagrant-disksize)
- [Vagrant vmware esxi plugin](https://github.com/josenk/vagrant-vmware-esxi)

```
Copyright (c) 2015-2021 The ISC Authors
