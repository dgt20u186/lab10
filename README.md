# lab10

```sh
$ export GITHUB_TOKEN=
$ export GITHUB_USERNAME=
$ export PACKAGE_MANAGER=apt
$ export GPG_PACKAGE_NAME=gpg
```

```sh
$ $PACKAGE_MANAGER install xclip #Устанавливаем утилиту xclip, предоставляющую доступ к буферу обмена Х из коммандной строки
$ alias gsed=sed
$ alias pbcopy='xclip -selection clipboard'
$ alias pbpaste='xclip -selection clipboard -o'
```

```sh
# Скачивание и установка пакета Go, для работы с релизами Github
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . 
$ source scripts/activate
$ go get github.com/aktau/github-release
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab09 projects/lab09
$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```

```sh
$ gsed -i 's/lab08/lab09/g' README.md
```

Устанавливаем GPG (утилиту для шифрования) и создаем новый секретный ключ
```sh
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
$ gpg --list-secret-keys --keyid-format LONG
$ gpg --full-generate-key
#Вводим секретный ключ
$ gpg --list-secret-keys --keyid-format LONG
$ gpg -K ${GITHUB_USERNAME}
# Сохраняем перменную с публичным ключом
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Сохраняем переменную с секретным ключом
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Выводим ключ в ASCII и копируем его в буфер обмена
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
$ pbpaste
# Вводим ключ в гитхабе
$ open https://github.com/settings/keys
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```

```sh
# Настраиваем скрипт для добавления сообщения к тегу
$ test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```

```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
$ cmake --build _build --target package
```

```sh
$ travis login --github-token (token)
$ travis enable
```

```sh
# Создание тега с сообщением с информацией а затем вервефеируем его
$ git tag -s v0.1.0.0
$ git tag -v v0.1.0.0
# Смотрим на изменения
$ git show v0.1.0.0
# Пушим
$ git push origin main --tags
```
Гитхаб релиз не сразу определялся как команда, но помог туториал с сайта https://www.digitalocean.com/community/tutorials/how-to-install-go-and-set-up-a-local-programming-environment-on-ubuntu-18-04-ru

а именно в файле ~/.profile
дописать 

export GOPATH=$HOME/go

export PATH=$PATH:$GOPATH/bin

export PATH=$PATH:$GOPATH/bin:/usr/local/go/bin

Создаем и заполняем релиз
```sh
$ github-release --version
$ github-release info -u ${GITHUB_USERNAME} -r lab09
$ github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"
```

```sh
# Дополнительно указываем ОС и архитектуру
$ export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m` 
$ export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
$ github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
```

```sh
$ github-release info -u ${GITHUB_USERNAME} -r lab09

#скачиваем и распаковываем наш релиз
$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}

$ tar -ztf ${PACKAGE_FILENAME}


dgt20u186@dgt20u186-Lenovo-G50-45:~/dgt20u186/workspace/projects/lab09$ tar -ztf ${PACKAGE_FILENAME}
print-0.1.0.0-Linux/lib/
print-0.1.0.0-Linux/lib/libprint.a
print-0.1.0.0-Linux/include/
print-0.1.0.0-Linux/include/print.hpp
print-0.1.0.0-Linux/bin/
print-0.1.0.0-Linux/bin/demo
print-0.1.0.0-Linux/cmake/
print-0.1.0.0-Linux/cmake/print-config-noconfig.cmake
print-0.1.0.0-Linux/cmake/print-config.cmake

```

