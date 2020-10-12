---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982456"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой DOCKER

Для целей тестирования используйте виртуальную машину Ubuntu с поддержкой DOCKER для доступа к реестру контейнеров Azure. Чтобы использовать Azure Active Directoryную проверку подлинности в реестре, также установите [Azure CLI][azure-cli] на виртуальной машине. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Вы можете использовать одну и ту же группу ресурсов для виртуальной машины и реестра контейнеров. Эта настройка упрощает очистку в конце, но не является обязательной. Если вы решили создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, выполните команду [AZ Group Create][az-group-create]. В следующем примере предполагается, что вы установили переменные среды для имени группы ресурсов и расположения реестра:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Теперь разверните виртуальную машину Ubuntu Azure по умолчанию с помощью команды [AZ VM Create][az-vm-create]. В следующем примере создается виртуальная машина с именем *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины может занять несколько минут. После выполнения команды запишите значение `publicIpAddress`, отображаемое Azure CLI. Этот адрес используется для установления SSH-подключений к виртуальной машине.

### <a name="install-docker-on-the-vm"></a>Установка Docker на виртуальной машине

После запуска виртуальной машины установите SSH-подключение к ней. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@publicIpAddress
```

Выполните следующие команды, чтобы установить DOCKER на виртуальной машине Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

После установки выполните следующую команду, чтобы проверить правильность работы Docker на виртуальной машине:

```bash
sudo docker run -it hello-world
```

Выходные данные:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Установка Azure CLI

Выполните действия, описанные в статье [Установка Azure CLI с помощью apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), чтобы установить Azure CLI на виртуальной машине Ubuntu. Пример:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Выйдите из SSH-подключения.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group