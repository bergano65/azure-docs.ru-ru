---
title: Краткое руководство по созданию виртуальной машины Linux с помощью Azure CLI | Документы Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure CLI создать виртуальную машину Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5dda96786cde980fbec1b2f90bb7f755453b468a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729665"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Краткое руководство. Создание виртуальной машины Linux с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом кратком руководстве показано, как с помощью Azure CLI развернуть в Azure виртуальную машину Linux. В этом руководстве мы установим 16.04 Ubuntu LTS. Чтобы проверить работу виртуальной машины, вы подключитесь к ней по протоколу SSH и установите веб-сервер NGINX.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блок кода. Вставьте код в Cloud Shell и нажмите клавишу ВВОД, чтобы выполнить его.

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm).

В следующем примере создается виртуальная машина *myVM* и добавляется учетная запись пользователя *azureuser*. Параметр `--generate-ssh-keys` автоматически создает ключ SSH и сохраняет его в стандартное расположение (*~/.ssh*). Чтобы использовать определенный набор ключей, примените параметр `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины и вспомогательных ресурсов занимает несколько минут. В следующем примере выходных данных показано, что виртуальная машина успешно создана.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение `publicIpAddress`, которое появится в выходных данных виртуальной машины. Этот адрес будет использоваться для доступа к виртуальной машине на следующих шагах.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

По умолчанию при создании виртуальной машины Linux в Azure открыты только SSH-подключения. Откройте TCP-порт 80, который понадобится для веб-сервера NGINX, с помощью команды [az vm open-port](/cli/azure/vm).

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

Установите SSH-подключение к виртуальной машине в обычном режиме. Замените **publicIpAddress** общедоступным IP-адресом виртуальной машины, который вы записали из выходных данных виртуальной машины на предыдущем шаге.

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Установка веб-сервера

Чтобы проверить работу виртуальной машины, установите веб-сервер NGINX. Обновите источники пакетов, а затем установите последнюю версию пакета NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

После этого введите `exit`, чтобы выйти из сеанса SSH.

## <a name="view-the-web-server-in-action"></a>Проверка работы веб-сервера

Страницу приветствия NGINX по умолчанию можно просмотреть в любом веб-браузере. Укажите общедоступный IP-адрес виртуальной машины как веб-адрес. В следующем примере показан сайт NGINX по умолчанию:

![Сайт NGINX по умолчанию](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group). 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы развернули простую виртуальную машину, открыли сетевой порт для веб-трафика и установили базовый веб-сервер. Дополнительные сведения о виртуальных машинах Azure см. в руководстве для виртуальных машин Linux.


> [!div class="nextstepaction"]
> [Создание виртуальных машин Linux и управление ими с помощью Azure CLI](./tutorial-manage-vm.md)
