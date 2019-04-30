---
title: Использование Docker Compose на виртуальной машине Linux в Azure | Документация Майкрософт
description: Как установить и использовать Docker и Compose на виртуальных машинах Linux с помощью Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474086"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Приступая к работе с Docker и Compose для определения и запуска многоконтейнерного приложения в Azure
Решение [Compose](https://github.com/docker/compose) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение будет развернуто с помощью одной команды, которая выполняет все действия, необходимые для развертывания определенной среды. В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB на виртуальной машине Ubuntu. Решение Compose можно использовать для настройки и более сложных приложений.

Последнее тестирование этой статьи выполнялось 14.02.2019 г. с помощью [Azure Cloud Shell](https://shell.azure.com/bash) и [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) версии 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Создание узлов Docker с помощью Azure CLI
Установите последнюю версию [Azure CLI](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index).

Сначала создайте группу ресурсов для среды Docker командой [az group create](/cli/azure/group). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Создайте файл *cloud-init.txt* и вставьте в него приведенную ниже конфигурацию. Введите `sensible-editor cloud-init.txt`, чтобы создать файл и просмотреть список доступных редакторов. 

```yaml
#include https://get.docker.com
```

Теперь создайте виртуальную машину командой [az vm create](/cli/azure/vm#az-vm-create). Используйте параметр `--custom-data`, чтобы передать файл конфигурации cloud-init. Укажите полный путь к конфигурации *cloud-init.txt*, если этот файл сохранен вне текущего рабочего каталога. В следующем примере выполняется создание виртуальной машины *myDockerVM* и открытие порта 80 для веб-трафика.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

На создание виртуальной машины, установку пакетов и запуск приложения может потребоваться несколько минут. Некоторые фоновые задачи продолжают работу после возврата к командной строке в Azure CLI. Когда виртуальная машина будет создана, запишите значение `publicIpAddress`, отображаемое Azure CLI. 

                 

## <a name="install-compose"></a>Установка Compose


Подключитесь к новой виртуальной машине узлу Docker по протоколу SSH. Введите собственный IP-адрес.

```bash
ssh azureuser@10.10.111.11
```

Установите Compose на виртуальную машину.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Создание файла конфигурации docker-compose.yml
Создайте файл конфигурации `docker-compose.yml`, определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере, необходимые переменные и зависимости среды, порты и ссылки между контейнерами. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу Compose](https://docs.docker.com/compose/compose-file/).

Создайте файл *docker-compose.yml*. Чтобы добавить в файл данные, используйте удобный для вас текстовый редактор. В следующем примере создается файл с запросом для `sensible-editor` на выбор редактора, который вы хотите использовать:

```bash
sensible-editor docker-compose.yml
```

Вставьте следующий пример в файл Docker Compose. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/) . Введите собственное значение *MYSQL_ROOT_PASSWORD*.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Запуск контейнеров с использованием Compose
В том же каталоге, где находится файл *docker-compose.yml*, выполните следующую команду (в зависимости от среды может потребоваться выполнить `docker-compose` с помощью `sudo`):

```bash
sudo docker-compose up -d
```

Эта команда запускает контейнеры Docker, указанные в файле *docker-compose.yml*. Выполнение этого этапа занимает одну-две минуты. Появится результат, аналогичный указанному ниже.

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Чтобы убедиться, что контейнеры работоспособны, введите `sudo docker-compose ps`. Вы увидите нечто вроде этого:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Теперь можно подключиться непосредственно к WordPress на виртуальной машине через порт 80. Откройте веб-браузер и введите IP-адрес виртуальной машины. Откроется начальный экран WordPress, позволяющий завершить установку и начать работу с приложением.

![Начальный экран WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справочнике по командной строке Compose](https://docs.docker.com/compose/reference/) и [руководстве пользователя](https://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](https://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, в шаблоне [Развертывание блога WordPress с помощью Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером Docker Swarm. Сценарии приведены в статье [Using Swarm with Compose](https://docs.docker.com/compose/swarm/) (Совместное использование Compose и Swarm).

