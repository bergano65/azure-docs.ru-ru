---
title: включение файла
description: включение файла
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185722"
---
### <a name="code-walkthrough"></a>Пошаговое руководство по написанию кода

В этом разделе описаны основные части примера кода и объясняется, как они связаны с акселератором решения для удаленного мониторинга.

В следующем фрагменте показано, как определяются сообщаемые свойства, описывающие возможности устройства. Эти свойства включают:

- Расположение устройства для включения акселератора решений, чтобы добавить устройство для сопоставления.
- Текущая версия встроенного ПО.
- Список методов, которые поддерживает устройство.
- Схема сообщений телеметрии, отправляемых устройством.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Пример включает функцию **serializeToJson**, которая выполняет сериализацию структуры данных, с помощью библиотеки Parson.

Пример включает несколько функций обратного вызова, которые выводят сведения на консоль в том случае,когда клиент взаимодействует с акселератором решений:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Эта функция обратного вызова **device_method_callback** показана в примере кода ниже. Эта функция определяет действие, выполняемое при получении вызова метода из комплекта акселератора решений. Функция получает ссылку на структуру данных **Chiller** в параметре **userContextCallback**. Значение **userContextCallback** устанавливается, когда функция обратного вызова настраивается в **основной** функции.

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Когда акселератор решения вызывает метод обновления встроенного ПО, пример десериализует полезные данные JSON и запускает фоновый поток для завершения процесса обновления. В следующем фрагменте представлен **do_firmware_update**, который выполняется в потоке.

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

В следующем фрагменте показано, как клиент отправляет сообщения телеметрии в акселератор решений. Свойства сообщения включают схему сообщений, чтобы дать возможность акселератору решений отобразить телеметрию на панели мониторинга:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

**Основная** функция в примере:

- Инициализирует и завершает работу подсистемы пакета SDK.
- Инициализирует структуру данных **Chiller**.
- Отправляет сообщаемые свойства в акселератор решений.
- Настраивает функцию метода обратного вызова устройства.
- Отправляет смоделированные значения телеметрии в акселератор решений.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
