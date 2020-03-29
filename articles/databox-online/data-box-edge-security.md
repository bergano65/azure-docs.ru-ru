---
title: Безопасность Azure Data Box Edge Документы Майкрософт
description: Описывает функции безопасности и конфиденциальности, которые защищают устройство, обслуживание и данные Azure Data Box Edge в помещении и в облаке.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970881"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Безопасность работы Azure Data Box Edge и защита данных

Безопасность является серьезной проблемой, когда вы принимаете новую технологию, особенно если технология используется с конфиденциальными или конфиденциальными данными. Azure Data Box Edge поможет вам гарантировать, что только авторизованные объекты могут просматривать, изменять или удалять ваши данные.

В этой статье описаны функции безопасности Data Box Edge, которые помогают защитить каждый из компонентов решения и данные, хранящиеся в них.

Azure Data Box Edge состоит из четырех основных компонентов, взаимодействующих друг с другом:

- **Служба Data Box Edge, размещенная в Azure**. Ресурс управления, который используется для создания заказа устройства, настройки устройства, а затем отслеживания заказа до завершения.
- **Устройство Data Box Edge**. Устройство передачи, которое поставляется вам, чтобы вы могли импортировать свои данные в Azure.
- **Клиенты/хозяева, подключенные к устройству.** Клиенты вашей инфраструктуры, которые подключаются к устройству Data Box Edge и содержат данные, которые необходимо защитить.
- **Облачное хранилище**. Местоположение на облачной платформе Azure, где хранятся данные. Это место обычно является учетной записью хранения, связанной с создаваемым ресурсом Data Box Edge.

## <a name="data-box-edge-service-protection"></a>Защита службы Data Box Edge

Служба Data Box Edge — это служба управления, размещенная в Azure. Служба используется для настройки и управления устройством.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Защита устройства Data Box Edge

Устройство Data Box Edge — это локальное устройство, которое помогает преобразовывать данные, обрабатывая их локально, а затем отправляя в Azure. Устройство:

- Для доступа к службе Data Box Edge требуется ключ активации.
- Всегда защищен паролем устройства.
- Является заблокированным устройством. Устройство BMC и BIOS защищены паролем. BIOS защищен ограниченным доступом к пользователю.
- Имеет безопасную загрузку включен.
- Запускает Windows Defender Device Guard. Device Guard позволяет запускать только доверенные приложения, которые вы определяете в политике целостности кода.

### <a name="protect-the-device-via-activation-key"></a>Защита устройства с помощью ключа активации

Только авторизованное устройство Data Box Edge может присоединиться к службе Data Box Edge, которую вы создаете в подписке Azure. Чтобы авторизовать устройство, необходимо использовать ключ активации для активации устройства с помощью службы Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Для получения дополнительной информации, смотрите [Получить ключ активации](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Защита устройства с помощью пароля

Пароли гарантируют, что только авторизованные пользователи могут получить доступ к вашим данным. Устройства Data Box Edge загружаются в заблокированном состоянии.

Вы можете:

- Подключитесь к локальному веб-интерфейсу устройства через браузер, а затем предоставьте пароль для вху-на-айфу на устройство.
- Удаленно подключитесь к интерфейсу PowerShell через HTTP. Дистанционное управление включено по умолчанию. Затем вы можете предоставить пароль устройства для вху-на-айму устройства. Для получения дополнительной информации [см.](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Используйте локальный веб-доступ для [изменения пароля.](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access) Если вы измените пароль, обязательно уведомите всех пользователей удаленного доступа, чтобы у них не было проблем с вводом.

## <a name="protect-your-data"></a>Защита ваших данных

В этом разделе описаны функции безопасности Data Box Edge, которые защищают транзитные и сохраненные данные.

### <a name="protect-data-at-rest"></a>Защита хранящихся данных

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Для защиты локальных данных используется 256-битное шифрование BitLocker XTS-AES.


### <a name="protect-data-in-flight"></a>Защита данных в полете

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Защита данных с помощью учетных записей хранения

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Регулярно вращайте и [синхронизируйте ключи учетной записи хранилища,](data-box-edge-manage-shares.md#sync-storage-keys) чтобы защитить учетную запись хранилища от неавторизованных пользователей.

## <a name="manage-personal-information"></a>Управление личной информацией

Служба Data Box Edge собирает личную информацию в следующих сценариях:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Чтобы просмотреть список пользователей, которые могут получить доступ к акции или удалить, выполните действия в [разделе Управления на Edge Data Box.](data-box-edge-manage-shares.md)

Для получения дополнительной информации просмотрите политику конфиденциальности корпорации Майкрософт в [Целевом центре.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание устройства Box Edge](data-box-edge-deploy-prep.md)
