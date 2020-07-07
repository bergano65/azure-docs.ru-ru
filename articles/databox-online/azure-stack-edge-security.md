---
title: Безопасность Azure Stack ребра | Документация Майкрософт
description: Описание функций безопасности и конфиденциальности, защищающих Azure Stack пограничных устройств, служб и данных в локальной среде и в облаке.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82570074"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack безопасность и защита данных на границе

Безопасность — это серьезная проблема при внедрении новой технологии, особенно если технология используется с конфиденциальными или собственными данными. Azure Stack ребро позволяет убедиться, что только разрешенные сущности могут просматривать, изменять или удалять данные.

В этой статье описываются функции безопасности пограничной Azure Stack, помогающие защитить каждый из компонентов решения и данные, хранящиеся в них.

Azure Stack ребро состоит из четырех основных компонентов, взаимодействующих друг с другом:

- **Служба пограничной службы Azure Stack, размещенная в Azure**. Ресурс управления, используемый для создания порядка устройств, настройки устройства, а затем отследить порядок до завершения.
- **Azure Stack пограничных устройств**. Устройство, которое вы передаете, чтобы импортировать локальные данные в Azure.
- **Клиенты и узлы, подключенные к устройству**. Клиенты в инфраструктуре, подключающиеся к Azure Stack пограничному устройству и содержащие данные, которые необходимо защитить.
- **Облачное хранилище**. Расположение на облачной платформе Azure, в которой хранятся данные. Это обычно учетная запись хранения, связанная с созданным ресурсом Azure Stack ребра.

## <a name="azure-stack-edge-service-protection"></a>Защита Azure Stack пограничной службы

Служба Azure Stack ребра — это служба управления, размещенная в Azure. Служба используется для настройки устройства и управления им.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack защита пограничных устройств

Azure Stack пограничное устройство — это локальное устройство, которое помогает преобразовать данные, обрабатывая их локально и отправив в Azure. Устройство:

- Требуется ключ активации для доступа к службе Azure Stack ребра.
- В любое время защищается паролем устройства.
- — Это заблокированное устройство. BMC и BIOS устройств защищаются паролем. BIOS защищена ограниченным доступом пользователей.
- Включена безопасная загрузка.
- Запускает Windows Defender Device Guard. Device Guard позволяет запускать только доверенные приложения, определенные в политиках целостности кода.

### <a name="protect-the-device-via-activation-key"></a>Защита устройства с помощью ключа активации

Только разрешенное Azure Stack пограничной устройству разрешено присоединяться к службе пограничной Azure Stack, созданной в подписке Azure. Для авторизации устройства необходимо использовать ключ активации, чтобы активировать устройство с помощью службы Azure Stack ребра.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Дополнительные сведения см. в разделе [Получение ключа активации](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Защита устройства с помощью пароля

Пароли гарантируют, что только полномочные пользователи могут получить доступ к вашим данным. Azure Stack пограничных устройств загружается в заблокированном состоянии.

Вы можете:

- Подключитесь к локальному веб-ИНТЕРФЕЙСу устройства через браузер, а затем укажите пароль для входа на устройство.
- Удаленное подключение к интерфейсу PowerShell устройства через HTTP. Удаленное управление включено по умолчанию. Затем можно указать пароль устройства для входа на устройство. Дополнительные сведения см. [в статье удаленное подключение к Azure Stack пограничному устройству](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Используйте локальный веб-интерфейс для [изменения пароля](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Если вы измените пароль, не забудьте уведомить всех пользователей удаленного доступа, чтобы у них не было проблем при входе в систему.

## <a name="protect-your-data"></a>Защита своих данных

В этом разделе описаны функции безопасности пограничной Azure Stack, защищающие транзитные и хранимые данные.

### <a name="protect-data-at-rest"></a>Защита хранящихся данных

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Шифрование BitLocker XTS-AES 256-bit используется для защиты локальных данных.


### <a name="protect-data-in-flight"></a>Защита данных в полете

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Защита данных с помощью учетных записей хранения

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Регулярно вращайте и [синхронизируйте ключи учетной записи хранения](azure-stack-edge-manage-shares.md#sync-storage-keys) , чтобы защитить учетную запись хранения от неавторизованных пользователей.

## <a name="manage-personal-information"></a>Управление личными сведениями

Служба Azure Stack ребра собирает персональные данные в следующих сценариях:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Чтобы просмотреть список пользователей, которые могут получить доступ к общей папке или удалить ее, выполните действия, описанные в разделе [Управление общими папками на Azure Stack пограничных](azure-stack-edge-manage-shares.md)устройствах.

Для получения дополнительных сведений ознакомьтесь с политикой конфиденциальности Майкрософт в [центре управления безопасностью](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание Azure Stack пограничной устройства](azure-stack-edge-deploy-prep.md)
