---
title: Мониторинг подключения устройств с помощью обозревателя Azure IoT Central
description: Отслеживайте сообщения с устройств и изменения двойников устройств с помощью интерфейса командной строки обозревателя IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 2b1c4c64fc02df67f38e36194072efd5db3b8e38
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953178"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Мониторинг подключения устройств с помощью Azure CLI (Предварительная версия компонентов)

*Этот раздел предназначен для разработчиков и администраторов.*

Используйте расширение Интернета вещей Azure CLI, чтобы просмотреть сообщения, отправляемые устройствами IoT Central и наблюдать за изменениями в двойникаах устройств. Это средство можно использовать для отладки и наблюдения за подключением устройств, а также для диагностики проблем, связанных с сообщениями устройств, которые не находятся в облаке или устройствах, не отвечающих на изменения двойника.

[Дополнительные сведения см. в справочнике по расширениям Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Технические условия

+ Azure CLI установлен и имеет версию 2.0.7 или более позднюю. Проверьте версию Azure CLI, выполнив `az --version`. Узнайте, как устанавливать и обновлять [документы Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Рабочая или учебная учетная запись в Azure, добавленная в качестве пользователя в IoT Centralое приложение.

## <a name="install-the-iot-central-extension"></a>Установка расширения IoT Central

Чтобы установить средство, в командной строке выполните следующую команду:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Проверьте версию расширения, выполнив 
```cmd/sh
az --version
```
Вы увидите расширение Azure-CLI-IOT-ext 0.8.1 или более поздней версии. Если это не так, запустите
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Использование расширения

В следующих разделах описаны общие команды и параметры, которые можно использовать при запуске `az iot central`. Чтобы просмотреть полный набор команд и параметров, передайте `--help` `az iot central` или любой из ее подкоманд.

### <a name="login"></a>Вход

Начните с входа в Azure CLI. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Получение идентификатора приложения IoT Centralного приложения
В окне **Администрирование/параметры приложения**скопируйте **идентификатор приложения**. Он будет использоваться в последующих шагах.

### <a name="monitor-messages"></a>Мониторинг сообщений
Отслеживайте сообщения, которые отправляются в приложение IoT Central с устройств. Будут включены все заголовки и заметки.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Просмотр свойств устройства
Просмотр текущих свойств чтения и чтения и записи для данного устройства.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать проводник IoT Central, предлагаем следующий шаг — изучение [управления устройствами IOT Central](howto-manage-devices.md).
