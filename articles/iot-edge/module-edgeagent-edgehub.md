---
title: Справочник по требуемым свойствам EdgeAgent и EdgeHub (Azure IoT Edge) | Документация Майкрософт
description: Обзор определенных свойств и их значений для двойников модулей EdgeAgent и EdgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ab45a6bde9ead69a7ea23dd095de84b8ff01334
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456704"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Свойства двойников модулей агента IoT Edge и центра IoT Edge

Агент IoT Edge и центр IoT Edge — это два модуля, которые составляют среду выполнения IoT Edge. Дополнительные сведения о функциях каждого модуля см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре (предварительная версия)](iot-edge-runtime.md). 

В этой статье представлены требуемые и отображаемые в отчете свойства двойников модулей для среды выполнения. For more information on how to deploy modules on IoT Edge devices, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

A module twin includes: 

* **Требуемые свойства**. The solution backend can set desired properties, and the module can read them. The module can also receive notifications of changes in the desired properties. Desired properties are used along with reported properties to synchronize module configuration or conditions.

* **Сообщаемые свойства**. The module can set reported properties, and the solution backend can read and query them. Reported properties are used along with desired properties to synchronize module configuration or conditions. 

## <a name="edgeagent-desired-properties"></a>Требуемые свойства EdgeAgent

Двойник модуля для агента IoT Edge называется `$edgeAgent`. Он координирует взаимодействие между агентом IoT Edge, выполняющимся на устройстве, и Центром Интернета вещей. Требуемые свойства задаются при применении манифеста развертывания на конкретном устройстве в ходе развертывания на одно устройство или несколько. 

| Свойство | Описание | Обязательно для заполнения |
| -------- | ----------- | -------- |
| schemaVersion | Должно быть "1.0". | ДА |
| runtime.type | Должно быть "docker". | ДА |
| runtime.settings.minDockerVersion | Задайте минимальную версию Docker, которая требуется для этого манифеста развертывания. | ДА |
| runtime.settings.loggingOptions | Переведенные в строку JSON, содержащую параметры ведения журнала для контейнера агента IoT Edge. [Параметры ведения журнала Docker](https://docs.docker.com/engine/admin/logging/overview/) | Нет |
| runtime.settings.registryCredentials<br>.{registryId}.username | Имя пользователя для реестра контейнеров. Для Реестра контейнеров Azure именем пользователя обычно является имя реестра.<br><br> Учетные данные реестра необходимы для любых образов модулей, не являющихся общедоступными. | Нет |
| runtime.settings.registryCredentials<br>.{registryId}.password | Пароль для реестра контейнеров. | Нет |
| runtime.settings.registryCredentials<br>.{registryId}.address | Адрес для реестра контейнеров. Для Реестра контейнеров Azure адрес обычно имеет вид *{имя реестра}.azurecr.io*. | Нет |  
| systemModules.edgeAgent.type | Должно быть "docker". | ДА |
| systemModules.edgeAgent.settings.image | Универсальный код ресурса (URI) образа агента IoT Edge. В настоящее время агент IoT Edge не может обновить себя сам. | ДА |
| systemModules.edgeAgent.settings<br>.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера агента IoT Edge. [Параметры создания Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Нет |
| systemModules.edgeAgent.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Центр Интернета вещей задает свойство при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |
| systemModules.edgeHub.type | Должно быть "docker". | ДА |
| systemModules.edgeHub.status | Должно быть "running". | ДА |
| systemModules.edgeHub.restartPolicy | Должно быть "always". | ДА |
| systemModules.edgeHub.settings.image | Универсальный код ресурса (URI) образа центра IoT Edge. | ДА |
| systemModules.edgeHub.settings<br>.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера центра IoT Edge. [Параметры создания Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Нет |
| systemModules.edgeHub.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Центр Интернета вещей задает свойство при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |
| modules.{ИД_модуля}.version | Определяемая пользователем строка, представляющая версию этого модуля. | ДА |
| modules.{ИД_модуля}.type | Должно быть "docker". | ДА |
| modules.{Ид_модуля}.status | {"running" \| "stopped"} | ДА |
| modules.{ИД_модуля}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | ДА |
| modules.{moduleId}.imagePullPolicy | {"on-create" \| "never"} | Нет |
| modules.{ИД_модуля}.settings.image | Универсальный код ресурса (URI) для образа модуля. | ДА |
| modules.{ИД_модуля}.settings.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера модуля. [Параметры создания Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Нет |
| modules.{ИД_модуля}.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Центр Интернета вещей задает свойство при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |

## <a name="edgeagent-reported-properties"></a>Отображаемые в отчете свойства EdgeAgent

Сообщаемые свойства агента IoT Edge включают в себя три основных элемента информации:

1. состояние применения последних требуемых свойств;
2. состояние модулей, запущенных на устройстве, зарегистрированное агентом IoT Edge;
3. копия требуемых свойств, выполняемых в данное время на устройстве.

This last piece of information, a copy of the current desired properties, is useful to tell whether the device has applied the latest desired properties or is still running a previous deployment manifest.

> [!NOTE]
> Сообщаемые свойства агента IoT Edge полезны, так как к ним можно выполнять запросы с помощью [языка запросов Центра Интернета вещей](../iot-hub/iot-hub-devguide-query-language.md), чтобы узнать состояние развертывания в масштабе. Дополнительные сведения об использовании свойств агента IoT Edge, чтобы узнать состояние развертывания, см. в статье [Общие сведения об автоматических развертываниях IoT Edge для отдельных устройств или в требуемом масштабе](module-deployment-monitoring.md).

Следующая таблица не включает сведения, которые копируются из требуемых свойств.

| Свойство | Описание |
| -------- | ----------- |
| lastDesiredVersion | Это целое число относится к последней версии требуемых свойств, обрабатываемых агентом IoT Edge. |
| lastDesiredStatus.code | This status code refers to the last desired properties seen by the IoT Edge agent. Допустимые значения: `200` — успех, `400` — недопустимая конфигурация,`412` — недопустимая версия схемы, `417` — нужные свойства пусты, `500` — сбой. |
| lastDesiredStatus.description | Текстовое описание состояния. |
| deviceHealth | Имеет значение `healthy`, если состояние среды выполнения всех модулей имеет значение `running` или `stopped`, в противном случае — `unhealthy`. |
| configurationHealth.{ИД_развертывания}.health | Имеет значение `healthy`, если состояние среды выполнения всех модулей, установленное развертыванием {ИД_развертывания}, имеет значение `running` или `stopped`, в противном случае — `unhealthy`. |
| runtime.platform.OS | Сообщает ОС, выполняющуюся на устройстве. |
| runtime.platform.architecture | Сообщает архитектуру ЦП на устройстве. |
| systemModules.edgeAgent.runtimeStatus | Состояние агента IoT Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Текстовое описание сообщаемого состояния агента IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Состояние центра IoT Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Текстовое описание состояния центра IoT Edge, если он неработоспособен. |
| systemModules.edgeHub.exitCode | The exit code reported by the IoT Edge hub container if the container exits |
| systemModules.edgeHub.startTimeUtc | Время последнего запуска центра IoT Edge. |
| systemModules.edgeHub.lastExitTimeUtc | Время последнего выхода центра IoT Edge. |
| systemModules.edgeHub.lastRestartTimeUtc | Время последнего перезапуска центра IoT Edge. |
| systemModules.edgeHub.restartCount | Количество раз, когда этот модуль был перезагружен в рамках политики перезапуска. |
| modules.{ИД_модуля}.runtimeStatus | Состояние модуля: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{ИД_модуля}.statusDescription | Текстовое описание состояния модуля, если он неработоспособен. |
| modules.{ИД_модуля}.exitCode | The exit code reported by the module container if the container exits |
| modules.{ИД_модуля}.startTimeUtc | Время последнего запуска модуля. |
| modules.{ИД_модуля}.lastExitTimeUtc | Время последнего завершения работы модуля. |
| modules.{ИД_модуля}.lastRestartTimeUtc | Время последнего перезапуска модуля. |
| modules.{ИД_модуля}.restartCount | Количество раз, когда этот модуль был перезагружен в рамках политики перезапуска. |

## <a name="edgehub-desired-properties"></a>Требуемые свойства EdgeHub

Двойник модуля центра IoT Edge называется `$edgeHub`. Он координирует взаимодействие между центром IoT Edge, выполняющимся на устройстве, и Центром Интернета вещей. Требуемые свойства задаются при применении манифеста развертывания на конкретном устройстве в ходе развертывания на одно устройство или несколько. 

| Свойство | Описание | Требуется в манифесте развертывания |
| -------- | ----------- | -------- |
| schemaVersion | Должно быть "1.0". | ДА |
| routes.{имя_маршрута} | Строка, представляющая маршрут центра IoT Edge. For more information, see [Declare routes](module-composition.md#declare-routes). | Элемент `routes` может присутствовать, но быть пустым. |
| storeAndForwardConfiguration.timeToLiveSecs | The time in seconds that IoT Edge hub keeps messages if disconnected from routing endpoints, whether IoT Hub or a local module. The value can be any positive integer. | ДА |

## <a name="edgehub-reported-properties"></a>Отображаемые в отчете свойства EdgeHub

| Свойство | Описание |
| -------- | ----------- |
| lastDesiredVersion | Это целое число относится к последней версии требуемых свойств, обрабатываемых центром IoT Edge. |
| lastDesiredStatus.code | The status code referring to last desired properties seen by the IoT Edge hub. Допустимые значения: `200` — успех, `400` — недопустимая конфигурация, `500` — сбой. |
| lastDesiredStatus.description | Text description of the status. |
| clients.{идентификатор устройства или модуля}.status | Состояние подключения этого устройства или модуля. Возможные значения {"connected" \| "disconnected"}. В отключенном состоянии могут находиться только удостоверения модуля. Подчиненные устройства, подключающиеся к центру IoT Edge, отображаются только при подключении. |
| clients.{идентификатор устройства или модуля}.lastConnectTime | Last time the device or module connected. |
| clients.{идентификатор устройства или модуля}.lastDisconnectTime | Last time the device or module disconnected. |

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как использовать эти свойства для создания манифестов развертывания, см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).
