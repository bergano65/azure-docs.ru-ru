---
title: Просмотр и настройка соединителя Интернета вещей Azure для метрик FHIR (предварительная версия)
description: В этой статье объясняется, как отобразить и настроить соединитель Azure IoT для метрик FHIR (Предварительная версия).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 1cdae789b8286be408735fff92e2de46e28ce514
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394294"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Просмотр и настройка соединителя Интернета вещей Azure для метрик FHIR (предварительная версия) 

В этой статье вы узнаете, как просматривать и настраивать соединитель Azure IoT для метрик FHIR *. 

> [!TIP]
> Следуйте указаниям в статье [Экспорт соединителя Azure IOT для FHIR (Предварительная версия) с помощью параметров диагностики](./iot-metrics-diagnostics-export.md) , чтобы узнать, как настроить экспорт данных метрик.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Просмотр метрик для соединителя Azure IoT для FHIR (Предварительная версия)
1. Чтобы просмотреть метрики для соединителей IoT, выберите службу API Azure для службы FHIR в портал Azure. 

2. Переход к **метрикам** 

3. Перейдите на вкладку **соединитель IOT** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Выберите соединитель IoT, чтобы просмотреть его метрики (например, существует (4) соединителей IoT, связанных с этим API Azure для службы FHIR).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Connector2 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> **Настраиваемая** вкладка позволяет создавать определенные сочетания времени и дат для просмотра метрик соединителя IOT.

5. Выберите период времени для отображаемых метрик соединителя Интернета вещей (например: 1 час, 24 часа, 7 дней или пользовательское).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Connector3 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Типы метрик для соединителя Azure IoT для FHIR (Предварительная версия) 
Ниже перечислены метрики отображаемого соединителя Интернета вещей.

|Тип метрик|Назначение метрик| 
|-----------|--------------|
|Число входящих сообщений|Число полученных необработанных входящих сообщений (например, событий устройства).|
|Число нормализованных сообщений|Число нормализованных сообщений.|
|Число групп сообщений|Число групп, у которых есть сообщения, агрегированные в указанное временное окно.|
|Средняя задержка нормализованного этапа|Средняя задержка этапа нормализации. Нормализация этапа — это выполнение нормализации необработанных входящих сообщений.|
|Средняя задержка этапа группы|Средняя задержка этапа группы. Этап группы заключается в выполнении буферизации, агрегирования и группирования нормализованных сообщений.| 
|общее число ошибок|Общее число ошибок.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Фокусирование и Настройка соединителя Azure IoT для метрик FHIR (Предварительная версия)
В этом примере мы будем сосредоточиться на **количестве метрик входящих сообщений** .

1. Выберите момент времени, на который вы хотите сосредоточиться.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Connector4 IoT" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. На этом экране вы можете **Добавить метрику** , **Добавить фильтр** и **Применить разбиение** для дальнейшей настройки. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Connector5 IoT" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Заключение 
Наличие доступа к метрикам плоскости данных очень важно для мониторинга и устранения неполадок.  Соединитель Azure IoT для FHIR помогает выполнять эти действия с помощью метрик. 

## <a name="next-steps"></a>Дальнейшие шаги

Ознакомьтесь с часто задаваемыми вопросами о соединителе Azure IoT для FHIR.

>[!div class="nextstepaction"]
>[Вопросы и ответы о соединителе Azure IoT для FHIR](fhir-faq.md)

*На портале Azure соединитель "Azure IoT для FHIR" называется соединителем IoT (предварительная версия).

FHIR — это зарегистрированная торговая марка организации HL7, которая используется с разрешения HL7.