---
title: Краткое руководство. Настройка и включение модуля безопасности для ОСРВ Azure
description: Узнайте, как подключить и включить модуль безопасности для службы ОСРВ Azure в Центре Интернета вещей Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 38e78b80fd14aec7557b563b2b33faf7d51bd114
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759738"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Краткое руководство. Модуль безопасности для ОСРВ Azure (предварительная версия)

В этой статье описаны предварительные требования для начала работы и объясняется, как включить модуль безопасности для службы ОСРВ Azure в Центре Интернета вещей. Если у вас сейчас нет Центра Интернета вещей, ознакомьтесь со статьей [Создание Центра Интернета вещей с помощью портала Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal), чтобы приступить к работе.

> [!NOTE]
> Модуль безопасности для ОСРВ Azure поддерживается только в Центрах Интернета вещей уровня "Стандартный".

## <a name="prerequisites"></a>Предварительные требования 

### <a name="supported-devices"></a>Поддерживаемые устройства

- пакет обнаружения ST STM32F746G;
- NXP i.MX RT1060 EVK;
- микросхема SAM E54 Xplained Pro EVK.

Скачайте, скомпилируйте и запустите один из ZIP-файлов для конкретной доски и средства (IAR, частичная IDE или ПК) по своему усмотрению из [ресурса GitHub для модуля безопасности ОСРВ Azure](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Ресурсы Azure

Далее мы подготовим ресурсы Azure. Вам потребуется Центр Интернета вещей. Мы рекомендуем использовать рабочую область Log Analytics. Для подключения к устройству потребуется строка подключения к Центру Интернета вещей. 
  
### <a name="iot-hub-connection"></a>Подключение к Центру Интернета вещей

Для начала работы требуется подключение к Центру Интернета вещей. 

1. Откройте **Центр Интернета вещей** на портале Azure.
1. Скопируйте строку подключения Интернета вещей в [файл конфигурации](how-to-azure-rtos-security-module.md).


Учетные данные подключений можно взять из конфигурации пользовательского приложения **HOST_NAME**, **DEVICE_ID** и **DEVICE_SYMMETRIC_KEY**.

Модуль безопасности для ОСРВ Azure использует подключения ПО промежуточного слоя Интернета вещей на основе протокола **MQTT**.


### <a name="log-analytics-workspace"></a>Рабочая область Log Analytics

Прием данных Log Analytics в Центре Интернета вещей отключен по умолчанию решением "Defender для Интернета вещей". Чтобы включить его для работы с модулем безопасности для ОСРВ Azure, выполните следующие действия: 
1. Найдите нужный Центр Интернета вещей на портале Azure.
1. Выберите **Настройки** в разделе меню **Безопасность**.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Доступ к параметру сбора данных для ОСРВ Azure"::: 
1. Выберите **Сбор данных**. 
1. В разделе **Конфигурация рабочей области** установите переключатель в положение **Вкл.** 
1. Создайте рабочую область Log Analytics или выберите существующую. Убедитесь, что установлен флажок **Access to raw security data** (Доступ к необработанным данным безопасности). 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Доступ к параметру сбора данных для ОСРВ Azure":::
1. Нажмите кнопку **Сохранить**.
1. Вернитесь к списку ресурсов Azure и убедитесь, что рабочая область Log Analytics (созданная или выбранная) включена для Центра Интернета вещей.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Доступ к параметру сбора данных для ОСРВ Azure"::: 

## <a name="next-steps"></a>Дальнейшие действия

Чтобы завершить конфигурацию и настройку вашего решения, перейдите к следующей статье.

> [!div class="nextstepaction"]
> [Настройка модуля безопасности для ОСРВ Azure](how-to-azure-rtos-security-module.md)
