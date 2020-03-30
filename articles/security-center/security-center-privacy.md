---
title: Управление данными пользователя в центре безопасности Azure | Документация Майкрософт
description: Узнайте, как управлять пользовательскими данными в Центре безопасности Azure. Управление данными пользователя включает в себя возможность доступа, удаления и экспорта данных.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 6edea1d0de53e2dc9f764de26209dc1f3110556e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978602"
---
# <a name="manage-user-data-in-azure-security-center"></a>Управление данными пользователя в центре безопасности Azure
В этой статье приводятся сведения об управлении данными пользователя в центре безопасности Azure. Управление данными пользователя включает в себя возможность доступа, удаления и экспорта данных.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Пользователь центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к данным клиента в рамках этого центра. Чтобы узнать больше о роли администратора учетной записи, см. встроенные [роли для управления доступом на основе ролей Azure,](../role-based-access-control/built-in-roles.md) чтобы узнать больше о ролях Reader, Owner и Contributor. Смотрите [администраторы подписки Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Поиск персональных данных и их идентификация
Пользователи Центра безопасности Azure могут просматривать свои персональные данные через портал Azure. В Центре безопасности Azure хранятся только сведения о контактных лицах по вопросам безопасности, такие как адреса электронной почты и номера телефонов. Для получения дополнительной информации смотрите [в Центре безопасности Azure.](security-center-provide-security-contact-details.md)

На портале Azure пользователь может просматривать разрешенные конфигурации IP-адресов, используя только что входную функцию доступа к VM Центра безопасности. Для получения дополнительной информации [см. Управление виртуальным доступом к машинам, используя точно в срок.](security-center-just-in-time.md)

На портале Azure пользователь может просмотреть оповещения безопасности, предоставляемые Центром безопасности Azure, включая сведения об IP-адресах и злоумышленниках. Для получения дополнительной информации в [Центре безопасности Azure](security-center-managing-and-responding-alerts.md)можно ознакомиться на см.

## <a name="classifying-personal-data"></a>Классификация персональных данных
Вам не нужно классифицировать личные данные, найденные в функции контакта безопасности Центра безопасности. Это адрес электронной почты (или несколько адресов) и номер телефона. [Контактные данные](security-center-provide-security-contact-details.md) проверяются Центром безопасности Azure.

Вам не нужно классифицировать IP-адреса и номера портов, сохраненные только в [срок](security-center-just-in-time.md) функцией Центра безопасности.

Только пользователь, которому назначена роль администратора, может классифицировать персональные данные, перейдя в окно [просмотра оповещений](security-center-managing-and-responding-alerts.md) в Центре безопасности Azure.

## <a name="securing-and-controlling-access-to-personal-data"></a>Защита доступа к персональным данным и управлением им
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к [данным контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md).

Пользователь Центра безопасности, назначенный на роль Reader, Owner, Contributor или Администратор аккаунта, может получить доступ к своим [политикам точно в срок.](security-center-just-in-time.md)

Пользователи Центра безопасности Azure, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут просматривать [оповещения](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Обновление персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может обновить [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) на портале Azure.

Пользователь Центра безопасности, которым назначена роль Владельца, Участника или Администратора учетной записи, может обновлять свои [политики точно в срок.](security-center-just-in-time.md)

Администратор учетной записи не может отсеивать инциденты оповещения. [Инцидент с оповещением](security-center-managing-and-responding-alerts.md) относится к данным безопасности и предназначен только для чтения.

## <a name="deleting-personal-data"></a>Удаление персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может удалить [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) через портал Azure.

Пользователь Центра безопасности, которым назначена роль Владельца, Автора или Администратора учетной записи, может удалить [политики как раз в времени](security-center-just-in-time.md) через портал Azure.

Пользователь Центра безопасности не может удалять оповещения. По соображениям безопасности [инцидент с оповещением](security-center-managing-and-responding-alerts.md) считается данными только для чтения.

## <a name="exporting-personal-data"></a>Экспорт персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может экспортировать [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) следующим образом:

- Копирование с портала Azure
- вызвав Azure REST API и используя метод GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Пользователь Центра безопасности, которому назначена роль Администратора учетной записи, может экспортировать [политики, содержащие IP-адреса,](security-center-just-in-time.md) по следующим вопросам:

- Копирование с портала Azure
- вызвав Azure REST API и используя метод GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Администратор учетной записи может экспортировать сведения об оповещении следующим образом:

- Копирование с портала Azure
- вызвав Azure REST API и используя метод GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Для получения дополнительной [информации, смотрите Получить оповещения безопасности (GET Коллекция)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ограничение использования персональных данных для профилирования или маркетинга без согласия
Пользователь Центра безопасности Azure может отказаться, удалив [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md).

[Данные](security-center-just-in-time.md) о своевременном доступе считаются неидентифицируемыми данными и сохраняются в течение 30 дней.

[Данные об оповещении](security-center-managing-and-responding-alerts.md) считаются данными безопасности и хранятся в течение двух лет.

## <a name="auditing-and-reporting"></a>Аудит и создание отчетов
В [журналах деятельности Azure](../azure-monitor/platform/platform-logs-overview.md)сохраняются журналы аудита контактов безопасности, точного времени и обновлений оповещения.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об управлении данными пользователя см. в статье об [управлении данными пользователя, обнаруженными в исследовании центра безопасности Azure](security-center-investigation-user-data.md).
