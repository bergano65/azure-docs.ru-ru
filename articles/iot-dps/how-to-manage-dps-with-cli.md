---
title: Управление службой подготовки устройств центра Интернета вещей с помощью Azure CLI & расширения IoT
description: Узнайте, как использовать Azure CLI и расширение Интернета вещей для управления службой подготовки устройств центра Интернета вещей (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678967"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Как управлять Службой подготовки устройств Центра Интернета вещей с помощью Azure CLI и расширения Интернета вещей

[Azure CLI](/cli/azure) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Azure CLI доступен в Windows, Linux и macOS. Azure CLI позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки.

Расширение Интернета вещей расширяет функции Azure CLI (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этом руководстве описаны настройки Azure CLI и расширения Интернета вещей. Затем вы узнаете, как запускать команды CLI для выполнения основных операций службы подготовки устройств. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Предварительные условия

- Необходимы [Python 2.7x или Python 3.x](https://www.python.org/downloads/).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Для работы с этой статьей требуется Azure CLI версии 2.0.70 или более поздней. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="basic-device-provisioning-service-operations"></a>Основные операции службы подготовки устройств

В примере показано, как войти в учетную запись Azure, создать группу ресурсов Azure (контейнер, содержащий связанные ресурсы для решения Azure), создать Центр Интернета вещей, службу подготовки устройств, список имеющихся служб подготовки устройств и связанный Центр Интернета вещей с командами CLI. 

Перед началом работы выполните описанные выше шаги по установке. Если у вас нет учетной записи Azure, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Войдите в учетную запись Azure.
  
```azurecli
az login
```

![На снимке экрана показано окно командной строки, в котором выполняется команда AZ Login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Создание группы ресурсов Иосубблогдемо в eastus

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Создать группу ресурсов](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Создание двух служб подготовки устройств

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Создание службы подготовки устройств](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Перечислите все существующие службы подготовки устройств в этой группе ресурсов.

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Создание списка службы подготовки устройств](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Создайте Блогдемохуб центра Интернета вещей в созданной группе ресурсов.

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Создание Центра Интернета вещей](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Связывание одного существующего центра Интернета вещей со службой подготовки устройств

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Связывание Центра Интернета вещей](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Следующие шаги
В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Регистрация устройства
> * запуск устройства;
> * проверка регистрации устройства.

Перейдите к следующему руководству, чтобы узнать, как подготовить несколько устройств через концентраторы с балансировкой нагрузки. 

> [!div class="nextstepaction"]
> [Подготовка устройств в Центрах Интернета вещей с балансировкой нагрузки](./tutorial-provision-multiple-hubs.md)