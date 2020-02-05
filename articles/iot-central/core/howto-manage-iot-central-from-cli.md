---
title: Управление IoT Central из Azure CLI | Документация Майкрософт
description: В этой статье описывается создание приложения IoT Central и управление им с помощью интерфейса командной строки. Вы можете просматривать, изменять и удалять приложения с помощью интерфейса командной строки.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8526eb50faf300892c66ac186eac25adecf62231
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019032"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central из Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Технические условия

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы предпочитаете запускать Azure CLI на локальном компьютере, см. статью [установка Azure CLI](/cli/azure/install-azure-cli). При запуске Azure CLI локально используйте команду **AZ login** для входа в Azure перед выполнением команд, описанных в этой статье.

## <a name="create-an-application"></a>Создание приложения

Используйте команду [AZ иотцентрал App Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) , чтобы создать приложение IOT Central в подписке Azure. Пример.

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Сначала эти команды создают группу ресурсов в регионе "Восточная часть США" для приложения. В следующей таблице описаны параметры, используемые командой **AZ иотцентрал App Create** .

| Параметр         | Description |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| location          | По умолчанию эта команда использует расположение из группы ресурсов. В настоящее время можно создать IoT Centralное приложение в **восточной части США**, **западной части США**, **Северной Европе**или **Западной Европе** , а также в географических регионах **Австралии** или **Азиатско-Тихоокеанский регион** . |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — https://mysubdomain.azureiotcentral.com. |
| sku               | В настоящее время можно использовать либо **ST1** , либо **ST2**. Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

**Шаблоны приложения**

| Имя шаблона            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства.
| iotc-pnp-preview@1.0.0   | Создает пустое приложение Plug and Play (Предварительная версия) для заполнения с помощью собственных шаблонов устройств и устройств. |
| iotc-condition@1.0.0     | Создает приложение с шаблоном мониторинга "аналитика в магазине". Этот шаблон используется для подключения и мониторинга среды хранения. |
| iotc-consumption@1.0.0   | Создает приложение с шаблоном мониторинга потребления воды. Этот шаблон используется для мониторинга потока воды и управления им. |
| iotc-distribution@1.0.0  | Создает приложение с шаблоном цифрового распространения. Используйте этот шаблон, чтобы повысить эффективность вывода данных в хранилище, дигитализинг ключевые активы и действия. |
| iotc-inventory@1.0.0     | Создает приложение с шаблоном управления интеллектуальным складом. Этот шаблон используется для автоматизации приема, перемещения продуктов, циклического подсчета и отслеживания датчиков. |
| iotc-logistics@1.0.0     | Создает приложение с подключенным шаблоном логистики. Этот шаблон используется для отслеживания отгрузки в реальном времени в сети, вода и земли с мониторингом расположения и условий. |
| iotc-meter@1.0.0         | Создает приложение с шаблоном интеллектуального мониторинга измерения. Этот шаблон используется для наблюдения за энергопотреблением, состоянием сети и выявления тенденций, повышающих эффективность поддержки клиентов и интеллектуального управления измерениями.  |
| iotc-patient@1.0.0       | Создание приложения с непрерывным шаблоном наблюдения за пациентами. Используйте этот шаблон, чтобы расширить здравоохранение, повторное распространение и управление заболеваниях. |
| iotc-power@1.0.0         | Создает приложение с шаблоном мониторинга солнечной панели. Этот шаблон используется для отслеживания состояния солнечной панели, тенденций создания энергии. |
| iotc-quality@1.0.0       | Создает приложение с шаблоном мониторинга качества воды. Используйте этот шаблон для цифрового мониторинга качества воды.|
| iotc-store@1.0.0         | Создает приложение с шаблоном "аналитика в магазине" — "извлечь". Этот шаблон используется для отслеживания потока извлечения в хранилище и управления им. |
| iotc-waste@1.0.0         | Создает приложение с подключенным шаблоном управления отходами. Этот шаблон используется для отслеживания расходных ячеек и операторов полей диспетчеризации. |

## <a name="view-your-applications"></a>Просмотр приложений

Используйте команду [AZ иотцентрал App List](/cli/azure/iotcentral/app#az-iotcentral-app-list) , чтобы получить список приложений IOT Central и просматривать метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Используйте команду [AZ иотцентрал App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) , чтобы обновить метаданные приложения IOT Central. Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Чтобы удалить IoT Central приложение, используйте команду [AZ иотцентрал App Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) . Пример.

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять приложениями IoT Central Azure из Azure CLI, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
