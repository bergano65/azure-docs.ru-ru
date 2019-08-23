---
title: Шлюз Azure Data Box безопасность | Документация Майкрософт
description: Описание функций безопасности и конфиденциальности, защищающих Шлюз Azure Data Box виртуального устройства, службы и данных в локальной среде и в облаке.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900602"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Шлюз Azure Data Box безопасность и защита данных

Безопасность — это серьезная проблема при внедрении новой технологии, особенно если технология используется с конфиденциальными или собственными данными. Шлюз Azure Data Box помогает убедиться, что только разрешенные сущности могут просматривать, изменять или удалять данные.

В этой статье описываются функции безопасности Шлюз Azure Data Box, помогающие защитить каждый из компонентов решения и данные, хранящиеся в них.

Шлюз Data Box решение состоит из четырех основных компонентов, взаимодействующих друг с другом:

- **Служба Шлюз Data Box, размещенная в Azure**. Ресурс управления, используемый для создания порядка устройств, настройки устройства, а затем отследить порядок до завершения.
- **Устройство шлюз Data Box**. Виртуальное устройство, которое вы подготавливаете в гипервизоре предоставленной вами системы. Это виртуальное устройство используется для импорта локальных данных в Azure.
- **Клиенты и узлы, подключенные к устройству**. Клиенты в инфраструктуре, которые подключаются к устройству Шлюз Data Box и содержат данные, требующие защиты.
- **Облачное хранилище**. Расположение на облачной платформе Azure, в которой хранятся данные. Это обычно учетная запись хранения, связанная с создаваемым ресурсом Шлюз Data Box.


## <a name="data-box-gateway-service-protection"></a>Защита Шлюз Data Box служб

Служба Шлюз Data Box — это служба управления, размещенная в Azure. Служба используется для настройки устройства и управления им.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Защита Шлюз Data Box устройства

Устройство Шлюз Data Box — это виртуальное устройство, подготовленное в низкоуровневой оболочке локальной системы, которую вы предоставляете. Устройство помогает отправить данные в Azure. Устройство:

- Для доступа к службе Data Box Edge и Шлюз Data Box требуется ключ активации.
- В любое время защищается паролем устройства.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Защита устройства с помощью ключа активации

Только разрешенному Шлюз Data Box устройству разрешено присоединяться к службе Шлюз Data Box, созданной в подписке Azure. Для авторизации устройства необходимо использовать ключ активации, чтобы активировать устройство со службой Шлюз Data Box.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Дополнительные сведения см. в разделе [Получение ключа активации](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Защита устройства с помощью пароля

Пароли гарантируют, что только полномочные пользователи могут получить доступ к вашим данным. Шлюз Data Box устройства загружаются в заблокированном состоянии.

Можно выполнить следующие действия:

- Подключитесь к локальному веб-ИНТЕРФЕЙСу устройства через браузер, а затем укажите пароль для входа на устройство.
- Удаленное подключение к интерфейсу PowerShell устройства через HTTP. Удаленное управление включено по умолчанию. Затем можно указать пароль устройства для входа на устройство. Дополнительные сведения см. [в статье удаленное подключение к устройству шлюз Data Box](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Используйте локальный веб-интерфейс для [изменения пароля](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Если вы измените пароль, не забудьте уведомить всех пользователей удаленного доступа, чтобы у них не было проблем при входе в систему.


## <a name="protect-your-data"></a>Защитите свои данные

В этом разделе описаны функции безопасности Шлюз Data Box, защищающие транзитные и хранимые данные.

### <a name="protect-data-at-rest"></a>Защита хранящихся данных

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Защита данных в полете

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Защита данных с помощью учетных записей хранения

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Регулярно вращайте и [синхронизируйте ключи учетной записи хранения](data-box-gateway-manage-shares.md#sync-storage-keys) , чтобы защитить учетную запись хранения от неавторизованных пользователей.

## <a name="manage-personal-information"></a>Управление личными сведениями

Служба Шлюз Data Box собирает персональные данные в следующих сценариях:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Чтобы просмотреть список пользователей, которые могут получить доступ к общей папке или удалить ее, выполните действия, описанные в разделе [Управление общими папками на шлюз Data Box](data-box-gateway-manage-shares.md).

Для получения дополнительных сведений ознакомьтесь с политикой конфиденциальности Майкрософт в [центре управления безопасностью](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Следующие шаги

[Развертывание устройства Шлюз Data Box](data-box-gateway-deploy-prep.md)
