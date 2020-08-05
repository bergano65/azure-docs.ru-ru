---
title: Соединитель Azure IoT для FHIR (Предварительная версия) — руководство по устранению неполадок и инструкции
description: Как устранить неполадки с общим соединителем Azure IoT для FHIR (Предварительная версия) сообщений об ошибках и условий и копирования файлов сопоставления
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553688"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Руководство по устранению неполадок в соединителе Azure IoT для FHIR (Предварительная версия)

В этой статье приводятся действия по устранению неполадок в сообщениях об ошибках и условиях FHIR * в соединителе Azure IoT.  

Вы также узнаете, как создавать копии соединителя Azure IoT для файлов сопоставления FHIR JSON для редактирования и архивации за пределами портал Azure или для предоставления технической поддержки Azure при открытии запроса в службу поддержки. 

## <a name="error-messages-and-fixes"></a>Сообщения об ошибках и исправления

|Сообщение   |Условие  |Fix         |
|----------|-----------|------------|
|Недопустимое имя сопоставления, имя сопоставления должно быть Device или FHIR|Указанный тип сопоставления не является устройством или FHIR|Используйте один из двух поддерживаемых типов сопоставления (например: Device или FHIR).|
|Повторное создание параметров ключа не определено|Запрос на повторное создание ключа|Включить параметры в запрос ключа повторного создания|
|Достигнуто максимальное число экземпляров соединителя IoT *, которые могут быть подготовлены в этой подписке.|Достигнута квота на использование соединителя Azure IoT для подписки FHIR (по умолчанию — 2) на подписку)|Удалите один из существующих экземпляров соединителя Azure IoT для FHIR, используйте другую подписку, которая не достигла квоты подписки, или запросите увеличение квоты подписки.|
|Перемещение ресурса не поддерживается для ресурса API Azure с включенным соединителем IoT для FHIR|Попытка выполнить операцию перемещения в ресурсе API Azure для FHIR, который содержит один или несколько экземпляров соединителя Azure IoT для FHIR|Удалите существующие экземпляры соединителя Azure IoT для FHIR и выполните операцию перемещения.|
|Соединитель IoT не подготовлен|Попытка использовать дочерние службы (подключения & сопоставления), если родительский объект (соединитель Azure IoT для FHIR) не был подготовлен|Подготавливает соединитель Azure IoT для FHIR|
|Запрос не поддерживается|Конкретный запрос API не поддерживается|Использование правильного запроса API|
|Учетная запись не существует|Попытка добавить соединитель Azure IoT для FHIR и API Azure для ресурса FHIR не существует.|Создание API Azure для ресурса FHIR и повторная попытка выполнения операции|
|Версия FHIR ресурсов API Azure для FHIR не поддерживается для соединителя IoT.|Попытка использовать соединитель Azure IoT для FHIR с несовместимой версией ресурса API Azure для FHIR|Создайте новый API Azure для ресурса FHIR (версия R4) или используйте существующий ресурс API Azure для FHIR (версия R4).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Создание копий файлов сопоставления соединителя Azure IoT для FHIR (Предварительная версия)
Копирование соединителя Azure IoT для файлов сопоставления FHIR может оказаться полезным для редактирования и архивации за пределами веб-сайта портал Azure и для предоставления технической поддержки Azure при открытии запроса в службу поддержки.

> [!NOTE]
> В настоящее время JSON — это единственный поддерживаемый формат для файлов сопоставления устройств и FHIR.

> [!TIP]
> Дополнительные сведения о соединителе Azure IoT для [устройств FHIR и FHIR СОПОСТАВЛЕНИЯ JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Выберите **"соединитель IOT (Предварительная версия)"** в левой нижней части панели мониторинга ресурсов API Azure для FHIR в разделе **"надстройки"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Соединитель IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Выберите **соединитель** , из которого будут копироваться файлы сопоставления.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Соединитель IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Этот процесс также может использоваться для копирования и сохранения содержимого JSON **"Configure FHIR Mapping"** .

3. Выберите **"настроить сопоставление устройств"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Соединитель IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Выберите содержимое JSON и выполните операцию копирования (например, нажмите клавиши Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Соединитель IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Выполните операцию вставки (например, выберите CTRL + v) в новый файл в редакторе (например, Visual Studio Code, Notepad) и сохраните файл с расширением *. JSON.

> [!TIP]
> Если вы откроете запрос в службу [технической поддержки Azure](https://azure.microsoft.com/support/create-ticket/) для соединителя Azure IOT для FHIR, обязательно включите копии файлов сопоставления, чтобы помочь в процессе устранения неполадок.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с часто задаваемыми вопросами о соединителе Azure IoT для FHIR.

>[!div class="nextstepaction"]
>[Вопросы и ответы о соединителе Azure IoT для FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* В портал Azure соединитель Azure IoT для FHIR называется соединителем IoT (Предварительная версия).

FHIR — это зарегистрированная торговая марка организации HL7, которая используется с разрешения HL7.