## Laboratory work IX

<a href="https://yandex.ru/efir/?stream_id=vYrKRcFKi46o"><img src="https://raw.githubusercontent.com/tp-labs/lab09/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Получить токен для доступа к репозиториям сервиса **GitHub**
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
# Указываем токен с доступом к репозиторию (и дополнительно к gist)
$ export GITHUB_TOKEN=****************************************
# Указываем имя пользователя Github
$ export GITHUB_USERNAME=qt-legion
# Указываем используемый пакетный менеджер
$ export PACKAGE_MANAGER=brew
# Указываем, в какой утилите будет создаваться ключ
$ export GPG_PACKAGE_NAME=gpg
```

```sh
# for *-nix system
# Устанавливаем утилиту xclip, предоставляющую доступ к буферу обмена Х из коммандной строки
$ $PACKAGE_MANAGER install xclip
$ alias gsed=sed
# Связывание команды pbcopy с записью данных в секцию clipboard
$ alias pbcopy='xclip -selection clipboard'
# Связывание команды pbpaste с чтением данных из секции clipboard
$ alias pbpaste='xclip -selection clipboard -o'
```

```sh
# Переходим в рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
# Сохраняем текущий каталог в виртуальный стек
$ pushd .
# Активация скрипта Node.js и rvm
$ source scripts/activate
# Скачивание и установка пакета Go, для работы с релизами Github
$ go get github.com/aktau/github-release

go: downloading github.com/aktau/github-release v0.10.0
go: downloading github.com/voxelbrain/goptions v0.0.0-20180630082107-58cddc247ea2
go: downloading github.com/dustin/go-humanize v1.0.0
go: downloading github.com/github-release/github-release v0.10.0
go: downloading github.com/tomnomnom/linkheader v0.0.0-20180905144013-02ca5825eb80
go: downloading github.com/kevinburke/rest v0.0.0-20210425173428-1fcb8c8e9022
```

```sh
# Подготавливаем репозитория lab09 для последующей работы
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
$ cd projects/lab09

Клонирование в «projects/lab09»…
remote: Enumerating objects: 91, done.
remote: Counting objects: 100% (91/91), done.
remote: Compressing objects: 100% (47/47), done.
remote: Total 91 (delta 28), reused 91 (delta 28), pack-reused 0
Получение объектов: 100% (91/91), 1.96 МиБ | 5.14 МиБ/с, готово.
Определение изменений: 100% (28/28), готово.

$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```

```sh
# Заменим все упоминая lab08 на lab09
$ gsed -i "" 's/lab08/lab09/g' README.md
```

```sh
# Устанавливаем программу GPG для шифрования информации и создания электронных цифровых подписей
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
# Проверяем на наличие ранее созданных ключей(подписей), путем показа существующих ключей
$ gpg --list-secret-keys --keyid-format LONG
# Гененрируем ключ, выбираем нужные параметры, которые предложит программа
$ gpg --full-generate-key
# Выводим в консоль сгенерированный ключ
$ gpg --list-secret-keys --keyid-format LONG

sec   rsa2048/**************** 2021-05-05 [SC] [   годен до: 2024-05-04]
      ****************************************
uid               [  абсолютно ] qt-legion (key_for_lab09_test) <my@email>
ssb   rsa2048/**************** 2021-05-05 [E] [   годен до: 2024-05-04]

$ gpg -K ${GITHUB_USERNAME}
# Создание переменной окружения с сохраненным в ней публичным ключом
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Создание переменной окружения с сохраненным в ней секретным ключом
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
# Выводим ключ в ASCII и копируем его в буфер
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
$ pbpaste

-----BEGIN PGP PUBLIC KEY BLOCK-----
...
-----END PGP PUBLIC KEY BLOCK-----

# Открываем вкладку в настройках Github, где устанавливают ключ
$ open https://github.com/settings/keys
# Добавляем секретный ключ в настройки Github
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```

```sh
# Настраиваем скрипт для добавления сообщения к тегу
$ test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```

```sh
# Генерируем пакет с раширением .tar.gz
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"

-- The C compiler identification is AppleClang 12.0.5.12050022
-- The CXX compiler identification is AppleClang 12.0.5.12050022
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/qt-legion/.hunter
-- [hunter] [ Hunter-ID: 5659b15 | Toolchain-ID: d9da15a | Config-ID: 65a58ee ]
-- [hunter] GTEST_ROOT: /home/qt-legion/.hunter/_Base/5659b15/d9da15a/65a58ee/Install (ver.: 1.10.0)
-- [hunter] Cache HIT: GTest
-- [hunter] Cache info: /home/qt-legion/.hunter/_Base/Cache/meta/d9da15a/GTest/1.10.0/9c89be7/a37fba7/4830b34/e5fa44f/da39a3e/cache.sha1
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/qt-legion/qt-legion/workspace/projects/lab09/_build

$ cmake --build _build --target package

[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
[ 75%] Building CXX object CMakeFiles/demo.dir/demo/main.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/qt-legion/qt-legion/workspace/projects/lab09/_build/print-0.1.0.0-Darwin.tar.gz generated.
```

```sh
# Логинемся в travis
$ travis login --auto
# Делаем проект доступным
$ travis enable

qt-legion/lab09: enabled :)
```

```sh
# Создание тега с сообщением с информацией
$ git tag -s v0.1.0.0
# Верефецируем тег
$ git tag -v v0.1.0.0
# Деменстрируем изменения
$ git show v0.1.0.0
# Отправляем измения на Github
$ git push origin master --tags

Перечисление объектов: 92, готово.
Подсчет объектов: 100% (92/92), готово.
При сжатии изменений используется до 4 потоков
Сжатие объектов: 100% (48/48), готово.
Запись объектов: 100% (92/92), 1.96 МиБ | 3.06 МиБ/с, готово.
Всего 92 (изменений 28), повторно использовано 91 (изменений 28), повторно использовано пакетов 0
remote: Resolving deltas: 100% (28/28), done.
To https://github.com/qt-legion/lab09
 * [new branch]      master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
```

```sh
$ github-release --version

github-release v0.9.0

$ github-release info -u ${GITHUB_USERNAME} -r lab09

- v0.1.0.0 (commit: https://api.github.com/repos/qt-legion/lab09/commits/735ac44e1c21663c2c8b7c97ca1695f4d9f56b50)
releases:
# Пока ничего нет, но будет

# Создание релиза
$ github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"
```

```sh
# Добавление артефакта с указанием ОС и архитектуры, на которых происходила компиляция библиотеки
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

tags:
- v0.1.0.0 (commit: https://api.github.com/repos/qt-legion/lab09/commits/735ac44e1c21663c2c8b7c97ca1695f4d9f56b50)
releases:
- v0.1.0.0, name: 'libprint', description: 'my first release', id: 42504418, tagged: 05/05/2021 at 21:14, published: 05/05/2021 at 21:20, draft: ✗, prerelease: ✗
  - artifact: print-Darwin-x86_64.tar.gz, downloads: 0, state: uploaded, type: application/octet-stream, size: 20 kB, id: 36472133

# Скачивание артефакта из раздела релизов для проверки
$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}

# Просмотр содержимого архива
$ tar -ztf ${PACKAGE_FILENAME}

print-0.1.0.0-Darwin/cmake/
print-0.1.0.0-Darwin/cmake/print-config-noconfig.cmake
print-0.1.0.0-Darwin/cmake/print-config.cmake
print-0.1.0.0-Darwin/bin/
print-0.1.0.0-Darwin/bin/demo
print-0.1.0.0-Darwin/include/
print-0.1.0.0-Darwin/include/print.hpp
print-0.1.0.0-Darwin/lib/
print-0.1.0.0-Darwin/lib/libprint.a
```

## Report

```sh
# Создание отчета
$ popd
$ export LAB_NUMBER=09
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Create Release](https://help.github.com/articles/creating-releases/)
- [Get GitHub Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
- [Signing Commits](https://help.github.com/articles/signing-commits-with-gpg/)
- [Go Setup](http://www.golangbootcamp.com/book/get_setup)
- [github-release](https://github.com/aktau/github-release)

```
Copyright (c) 2015-2020 The ISC Authors
```
