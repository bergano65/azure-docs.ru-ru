---
title: Мониторинг подключения устройств с помощью обозревателя Azure IoT Central
description: Отслеживайте сообщения с устройств и изменения двойников устройств с помощью интерфейса командной строки обозревателя IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756665"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Мониторинг подключения устройств с помощью Azure CLI

*Этот раздел относится к разработчикам устройств и сборщикам решений.*

Используйте расширение Интернета вещей Azure CLI, чтобы просмотреть сообщения, отправляемые устройствами IoT Central и наблюдать за изменениями в двойникаах устройств. Это средство можно использовать для отладки и наблюдения за подключением устройств, а также для диагностики проблем, связанных с сообщениями устройств, которые не находятся в облаке или устройствах, не отвечающих на изменения двойника.

[Дополнительные сведения см. в справочнике по расширениям Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Предварительные условия

+ Azure CLI установлен и имеет версию 2.0.7 или более позднюю. Проверьте версию Azure CLI, выполнив `az --version`. Узнайте, как устанавливать и обновлять [документы Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Рабочая или учебная учетная запись в Azure, добавленная в качестве пользователя в IoT Centralое приложение.

## <a name="install-the-iot-central-extension"></a>Установка расширения IoT Central

Чтобы установить средство, в командной строке выполните следующую команду:

```azurecli
az extension add --name azure-iot
```

Проверьте версию расширения, выполнив команду:

```azurecli
az --version
```

Вы должны увидеть расширение Azure-IOT 0.8.1 или более поздней версии. Если это не так, выполните:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Использование расширения

В следующих разделах описаны общие команды и параметры, которые можно использовать при запуске `az iot central`. Чтобы просмотреть полный набор команд и параметров, передайте `--help` его подкоманды `az iot central` или любую из ее подкоманд.

### <a name="login"></a>Имя входа

Начните с входа в Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Получение идентификатора приложения IoT Centralного приложения
В окне **Администрирование/параметры приложения**скопируйте **идентификатор приложения**. Это значение используется в последующих шагах.

### <a name="monitor-messages"></a>Мониторинг сообщений
Отслеживайте сообщения, которые отправляются в приложение IoT Central с устройств. Выходные данные включают все заголовки и заметки.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Просмотр свойств устройства
Просмотр текущих свойств чтения и чтения и записи для данного устройства.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Дальнейшие шаги

Если вы являетесь разработчиком устройства, рекомендуем следующий шаг — прочитать сведения о [подключении устройства в Azure IOT Central](./concepts-get-connected.md).
