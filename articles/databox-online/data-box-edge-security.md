---
title: Azure Data Box Edge безопасность | Документация Майкрософт
description: Описание функций безопасности и конфиденциальности, защищающих ваше Azure Data Box Edge устройство, службу и данные в локальной среде и в облаке.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970881"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge безопасность и защита данных

Безопасность — это серьезная проблема при внедрении новой технологии, особенно если технология используется с конфиденциальными или собственными данными. Azure Data Box Edge помогает убедиться, что только разрешенные сущности могут просматривать, изменять или удалять данные.

В этой статье описываются функции безопасности Data Box Edge, помогающие защитить каждый из компонентов решения и данные, хранящиеся в них.

Azure Data Box Edge состоит из четырех основных компонентов, взаимодействующих друг с другом:

- **Служба Data Box EDGE, размещенная в Azure**. Ресурс управления, используемый для создания порядка устройств, настройки устройства, а затем отследить порядок до завершения.
- **Устройство Data Box Edge**. Устройство, которое вы передаете, чтобы импортировать локальные данные в Azure.
- **Клиенты и узлы, подключенные к устройству**. Клиенты в инфраструктуре, которые подключаются к устройству Data Box Edge и содержат данные, требующие защиты.
- **Облачное хранилище**. Расположение на облачной платформе Azure, в которой хранятся данные. Это обычно учетная запись хранения, связанная с создаваемым ресурсом Data Box Edge.

## <a name="data-box-edge-service-protection"></a>Защита Data Box Edge служб

Служба Data Box Edge — это служба управления, размещенная в Azure. Служба используется для настройки устройства и управления им.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Защита Data Box Edge устройства

Устройство Data Box Edge — это локальное устройство, которое помогает преобразовать данные, обрабатывая их локально и отправив в Azure. Устройство:

- Для доступа к службе Data Box Edge требуется ключ активации.
- В любое время защищается паролем устройства.
- — Это заблокированное устройство. BMC и BIOS устройств защищаются паролем. BIOS защищена ограниченным доступом пользователей.
- Включена безопасная загрузка.
- Запускает Windows Defender Device Guard. Device Guard позволяет запускать только доверенные приложения, определенные в политиках целостности кода.

### <a name="protect-the-device-via-activation-key"></a>Защита устройства с помощью ключа активации

Только разрешенному Data Box Edge устройству разрешено присоединяться к службе Data Box Edge, созданной в подписке Azure. Для авторизации устройства необходимо использовать ключ активации, чтобы активировать устройство со службой Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Дополнительные сведения см. в разделе [Получение ключа активации](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Защита устройства с помощью пароля

Пароли гарантируют, что только полномочные пользователи могут получить доступ к вашим данным. Data Box Edge устройства загружаются в заблокированном состоянии.

Можно выполнить следующие действия:

- Подключитесь к локальному веб-ИНТЕРФЕЙСу устройства через браузер, а затем укажите пароль для входа на устройство.
- Удаленное подключение к интерфейсу PowerShell устройства через HTTP. Удаленное управление включено по умолчанию. Затем можно указать пароль устройства для входа на устройство. Дополнительные сведения см. [в статье удаленное подключение к устройству Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Используйте локальный веб-интерфейс для [изменения пароля](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Если вы измените пароль, не забудьте уведомить всех пользователей удаленного доступа, чтобы у них не было проблем при входе в систему.

## <a name="protect-your-data"></a>Защитите свои данные

В этом разделе описаны функции безопасности Data Box Edge, защищающие транзитные и хранимые данные.

### <a name="protect-data-at-rest"></a>Защита хранящихся данных

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Шифрование BitLocker XTS-AES 256-bit используется для защиты локальных данных.


### <a name="protect-data-in-flight"></a>Защита данных в полете

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Защита данных с помощью учетных записей хранения

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Регулярно вращайте и [синхронизируйте ключи учетной записи хранения](data-box-edge-manage-shares.md#sync-storage-keys) , чтобы защитить учетную запись хранения от неавторизованных пользователей.

## <a name="manage-personal-information"></a>Управление личными сведениями

Служба Data Box Edge собирает персональные данные в следующих сценариях:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Чтобы просмотреть список пользователей, которые могут получить доступ к общей папке или удалить ее, выполните действия, описанные в разделе [Управление общими папками на Data Box Edge](data-box-edge-manage-shares.md).

Для получения дополнительных сведений ознакомьтесь с политикой конфиденциальности Майкрософт в [центре управления безопасностью](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Следующие шаги

[Развертывание устройства Data Box Edge](data-box-edge-deploy-prep.md)
