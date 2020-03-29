---
title: Поймите поддержку службы предоставления устройств Azure IoT в поддержку МЗТТ Документы Майкрософт
description: Руководство для разработчиков - поддержка устройств, подключенных к службе обеспечения устройств Azure IoT (DPS) с помощью конечная точка СМТТ.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973379"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Общайтесь с DPS с помощью протокола МЗТТ

DPS позволяет устройствам общаться с конечным пунктом устройства DPS с помощью:

* протокола [MQTT версии 3.1.1](https://mqtt.org/) на порту 8883;
* [МЗТТ v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) над WebSocket на порту 443.

DPS не является полнофункциональным брокером МЗТТ и не поддерживает все виды поведения, указанные в стандарте МЗТТ v3.1.1. В этой статье описывается, как устройства могут использовать поддерживаемые поведения МЗТТ для связи с DPS.

Все устройства связи с DPS должны быть защищены с помощью TLS / SSL. Таким образом, DPS не поддерживает небезопасные соединения над портом 1883.

 > [!NOTE] 
 > В настоящее время DPS не поддерживает устройства, использующие [механизм проверки](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) TPM по протоколу МЗТТ.

## <a name="connecting-to-dps"></a>Подключение к DPS

Устройство может использовать протокол МЗТТ для подключения к DPS, используя любой из следующих вариантов.

* Библиотеки в [SDK Azure IoT.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)
* Протокол МЗТТ напрямую.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Непосредственное использование протокола MQTT (как устройство)

Если устройство не может использовать пакеты SDK для устройств, оно может подключаться к общедоступным конечным точкам устройства по протоколу MQTT по порту 8883. В пакете **CONNECT** устройство должно использовать следующие значения:

* Для поля **ClientId** используйте **registrationId**.

* Для поля **Username** `{idScope}/registrations/{registration_id}/api-version=2019-03-31`используйте, где `{idScope}` находится [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) DPS.

* В поле **Пароль** укажите маркер SAS. Формат маркера SAS аналогичен описанному для протоколов HTTPS и AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`Ресурс должен быть в `{idScope}/registrations/{registration_id}`формате. Имя политики `registration`должно быть .

  > [!NOTE]
  > При использовании аутентификации с помощью сертификата X.509 пароли маркеров SAS не требуются.

  Для получения дополнительной информации о том, как создавать токены [Control access to DPS](how-to-control-access.md#security-tokens)SAS, см.

Ниже приводится список поведения, конкретного для реализации DPS:

 * DPS не поддерживает функциональность флага **CleanSession,** установленного на **0**.

 * Когда приложение устройства подписывается на тему с **помощью qoS 2**, DPS предоставляет максимальный уровень 1 в пакете **SUBACK.** После этого DPS доставляет сообщения на устройство с помощью qoS 1.

## <a name="tlsssl-configuration"></a>конфигурация протокола TLS/SSL

Чтобы использовать протокол МЗТТ напрямую, клиент *должен* подключиться по TLS 1.2. Попытки пропустить этот шаг будут завершаться ошибками соединения.


## <a name="registering-a-device"></a>Регистрация устройства

Чтобы зарегистрировать устройство через DPS, устройство `$dps/registrations/res/#` должно подписаться, используя в качестве **фильтра темы.** Многоуровневый подстановочный знак `#` в параметре Topic Filter (Фильтр разделов) используется только для того, чтобы разрешить устройству получать дополнительные свойства в имени раздела. DPS не разрешает использование `#` подстановочных знаков для `?` фильтрации подтопов. Поскольку DPS не является брокером обмена сообщениями в пабе общего назначения, он поддерживает только задокументированные имена тем и тематические фильтры.

Устройство должно публиковать регистрационное сообщение `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` dPS, используя в качестве **имя темы.** Полезная нагрузка должна содержать объект [регистрации устройств](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) в формате JSON.
В успешном сценарии устройство получит ответ `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` на имя темы, где x является значением retry-after в считанные секунды. Полезная нагрузка ответа будет содержать объект [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) в формате JSON.

## <a name="polling-for-registration-operation-status"></a>Опрос для статуса регистрационной операции

Устройство должно периодически опрашивать службу, чтобы получить результат операции регистрации устройства. Предполагая, что устройство уже `$dps/registrations/res/#` подписалось на тему, как указано выше, `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` оно может опубликовать сообщение о состоянии операции на имя темы. Идентификатор операции в этом сообщении должен быть значением, полученным в сообщении ответа RegistrationOperationStatus на предыдущем этапе. В успешном случае служба ответит `$dps/registrations/res/200/?$rid={request_id}` на эту тему. Полезная нагрузка ответа будет содержать объект RegistrationOperationStatus. Устройство должно продолжать опрос службы, если код ответа 202 после задержки, равной периоду повторной попытки. Операция регистрации устройства успешна, если служба возвращает 200 статусный код.

## <a name="connecting-over-websocket"></a>Подключение через Websocket
При подключении через Websocket, `mqtt`укажите подпротокол как . Следуйте [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о протоколе MQTT см. в [документации по MQTT](https://mqtt.org/documentation).

Для дальнейшего изучения возможностей DPS см.:

* [О IoT DPS](about-iot-dps.md)
