---
title: Безопасность шлюза коробки данных Azure (ru) Документы Майкрософт
description: Описывает функции безопасности и конфиденциальности, которые защищают виртуальное устройство, обслуживание и данные Azure Data Box Gateway, на месте и в облаке.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900602"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Безопасность и защита данных Azure Data Box Gateway

Безопасность является серьезной проблемой, когда вы принимаете новую технологию, особенно если технология используется с конфиденциальными или конфиденциальными данными. Шлюз Azure Data Box поможет вам гарантировать, что только авторизованные объекты могут просматривать, изменять или удалять ваши данные.

В этой статье описаны функции безопасности Azure Data Box Gateway, которые помогают защитить каждый из компонентов решения и хранящиеся в них данные.

Решение Data Box Gateway состоит из четырех основных компонентов, взаимодействующих друг с другом:

- **Служба шлюза Data Box, размещенная в Azure**. Ресурс управления, который используется для создания заказа устройства, настройки устройства, а затем отслеживания заказа до завершения.
- **Устройство Data Box Gateway**. Виртуальное устройство, которое вы предоставляете в гипервизор системы, которую вы предоставляете. Это виртуальное устройство используется для импорта данных в Azure.
- **Клиенты/хозяева, подключенные к устройству.** Клиенты вашей инфраструктуры, которые подключаются к устройству Data Box Gateway и содержат данные, которые необходимо защитить.
- **Облачное хранилище**. Местоположение на облачной платформе Azure, где хранятся данные. Это место обычно является учетной записью хранения, связанной с создаваемым ресурсом Data Box Gateway.


## <a name="data-box-gateway-service-protection"></a>Защита службы Data Box Gateway

Служба data Box Gateway — это служба управления, размещенная в Azure. Служба используется для настройки и управления устройством.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Защита устройства Data Box Gateway

Устройство Data Box Gateway — это виртуальное устройство, которое подготовлено в гипервизоре предоставляемой вами системы. Устройство помогает отправлять данные в Azure. Устройство:

- Для доступа к службе Data Box Edge/Data Box Gateway требуется ключ активации.
- Всегда защищен паролем устройства.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Защита устройства с помощью ключа активации

Только авторизованное устройство Data Box Gateway может присоединиться к службе Data Box Gateway, которую вы создаете в подписке Azure. Чтобы авторизовать устройство, необходимо использовать ключ активации для активации устройства с помощью службы Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Для получения дополнительной информации, смотрите [Получить ключ активации](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Защита устройства с помощью пароля

Пароли гарантируют, что только авторизованные пользователи могут получить доступ к вашим данным. Устройства Data Box Gateway загружаются в заблокированном состоянии.

Вы можете:

- Подключитесь к локальному веб-интерфейсу устройства через браузер, а затем предоставьте пароль для вху-на-айфу на устройство.
- Удаленно подключиться к интерфейсу PowerShell устройства через HTTP. Дистанционное управление включено по умолчанию. Затем вы можете предоставить пароль устройства для вху-на-айму устройства. Для получения дополнительной информации [см.](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Используйте локальный веб-доступ для [изменения пароля.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) Если вы измените пароль, обязательно уведомите всех пользователей удаленного доступа, чтобы у них не было проблем с вводом.


## <a name="protect-your-data"></a>Защита ваших данных

В этом разделе описаны функции безопасности Data Box Gateway, которые защищают транзитные и сохраненные данные.

### <a name="protect-data-at-rest"></a>Защита хранящихся данных

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Защита данных в полете

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Защита данных с помощью учетных записей хранения

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Регулярно вращайте и [синхронизируйте ключи учетной записи хранилища,](data-box-gateway-manage-shares.md#sync-storage-keys) чтобы защитить учетную запись хранилища от неавторизованных пользователей.

## <a name="manage-personal-information"></a>Управление личной информацией

Служба Data Box Gateway собирает личную информацию в следующих сценариях:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Чтобы просмотреть список пользователей, которые могут получить доступ к акции или удалить, выполните действия в [разделе Управления на шлюзе Box Data.](data-box-gateway-manage-shares.md)

Для получения дополнительной информации просмотрите политику конфиденциальности корпорации Майкрософт в [Целевом центре.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание устройства шлюза Box Data](data-box-gateway-deploy-prep.md)
