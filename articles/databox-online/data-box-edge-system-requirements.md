---
title: Требования к системе для Azure Data Box Edge | Документация Майкрософт
description: Дополнительные сведения о требованиях к программному обеспечению и сети для Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: 720f4433c1e7b0d271e5729236f439c92a84687e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884658"
---
# <a name="azure-data-box-edge-system-requirements"></a>Требования к системе Azure Edge поле данных

В этой статье описаны важные требования к системе и рекомендации для решения на основе шлюза Azure Data Box Edge и для клиентов, подключающихся к нему. Перед началом развертывания Data Box Edge рекомендуется ознакомиться со следующими сведениями. Данные сведения можно просматривать во время развертывания и при последующих операциях.

Требования к системе Data Box Edge.

- **Требования к программному обеспечению для узлов**. Описание поддерживаемых платформ, браузеров для локальной конфигурации пользовательского интерфейса, клиентов SMB и любых дополнительных требований для клиентов, которые получают доступ к устройству.
- **Требования к сети для устройства**. Предоставление информации о любых сетевых требованиях к работе физическое устройства.

## <a name="supported-os-for-clients-connected-to-device"></a>Поддерживаемая ОС для клиентов, подключенных к устройству

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Поддерживаемые протоколы для клиентов, обращающихся к устройству

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Поддерживаемые типы хранилищ

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Поддерживаемые браузеры для локального пользовательского веб-интерфейса

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Обязательные сетевые порты

### <a name="port-requirements-for-data-box-edge"></a>Требования к портам для Data Box Edge

В таблице ниже приведены порты, которые должны быть открытыми в брандмауэре для разрешения использования SMB, облака или управления трафиком. В этой таблице значение *в* или *входящий* относится к направлению, из которого клиент запрашивает доступ к вашему устройству. Значение *исходящий* указывает на *направление*, в котором устройство Data Box Edge отправляет данные за пределами развертывания, например, в Интернет.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Требования к портам для IoT Edge

Azure IoT Edge обеспечивает исходящее подключение локального пограничного устройства с облаком Azure с помощью поддерживаемых протоколов Центра Интернета вещей. Входящий трафик требуется только для конкретных сценариев, в которых Центр Интернета вещей отправляет сообщения на устройство Azure IoT Edge (например, обмен сообщениями между облаком и устройством).

Чтобы получить конфигурацию порта для серверов, на которых размещается среда выполнения Azure IoT Edge, используйте следующую таблицу.

| Порт № | Входящий или исходящий | Область порта | Обязательно для заполнения | Руководство |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Исходящий       | WAN        | Yes      | Исходящие подключение открыто для службы IoT Edge (при подготовке). Эта конфигурация необходима при использовании созданных вручную сценариев или Службы подготовки устройств к добавлению в Центр Интернета вещей Azure.|

Дополнительные сведения см. в статье [Распространенные проблемы и их решения для Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Шаблоны URL-адресов для правил брандмауэра

Нередко сетевые администраторы могут настраивать правила брандмауэра на основе шаблонов URL-адресов, чтобы фильтровать входящий и исходящий трафик. Устройство и служба Data Box Edge зависят от других приложений Майкрософт, в том числе от служебной шины Microsoft Azure, контроля доступа Azure Active Directory, учетных записей хранения и серверов центра обновления Майкрософт. Шаблоны URL-адресов, связанных с этими приложениями, можно использовать для настройки правил брандмауэра. Важно понимать, что шаблоны URL-адресов, связанных с этими приложениями, могут меняться. Данные изменения требуют от сетевого администратора отслеживать правила брандмауэра для Data Box Edge и обновлять их по мере необходимости.

В большинстве случаев мы рекомендуем настраивать правила брандмауэра для исходящего трафика на основе фиксированных IP-адресов Data Box Edge. Тем не менее, чтобы задать расширенные правила брандмауэра, необходимые для создания безопасных сред, можно использовать приведенные ниже сведения.

> [!NOTE]
> - В качестве IP-адресов устройств (источников) всегда должны устанавливаться все сетевые интерфейсы с поддержкой облака.
> - В качестве IP-адресов назначения должны устанавливаться [диапазоны IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Шаблоны URL-адресов для функции шлюза

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Шаблоны URL-адресов для функции вычисления

| Шаблон URL-адреса                      | Компонент или функциональность                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Реестр контейнеров Майкрософт (обязательный)               |   |
| https://\*.azurecr.io                     | Реестры личных и сторонних контейнеров (необязательно) |   |
| https://\*.azure-devices.net              | Политика доступа Центра Интернета вещей (обязательно)                             |   |

### <a name="url-patterns-for-azure-government"></a>Шаблоны URL-адресов для Azure для государственных организаций

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Пропускная способность Интернета

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Дальнейшие действия

- [Развертывание в Edge поле данных Azure](data-box-edge-deploy-prep.md)
