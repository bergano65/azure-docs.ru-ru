---
title: 'Azure Active Directory: лицензирование самостоятельного сброса пароля'
description: Узнайте о разнице требований к сбою паролем в функциональности Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393065"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Требования к лицензированию для сбова пароля самообслуживания Azure Active Directory

Чтобы уменьшить количество вызовов в службу поддержки и потерю производительности, когда пользователь не может войти в систему на свое устройство или приложение, учетные записи пользователей в Azure Active Directory (Azure AD) могут быть включены для сбоя пароля самообслуживания (SSPR). Функции, входят в состав SSPR, включают изменение пароля, сбросить, разблокировать и списать в каталог. Основные функции SSPR доступны для пользователей Office 365 и всех пользователей Azure AD бесплатно.

В этой статье подробно описаны различные способы, сброс паролем самообслуживания, которые могут быть лицензированы и использованы. Для получения подробной информации о ценах и выставлении счетов можно ознакомиться на [странице ценообразования Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="compare-editions-and-features"></a>Сравнение выпусков и функций

SSPR лицензируется на одного пользователя. Для поддержания соответствия требованиям организации обязаны назначать соответствующую лицензию своим пользователям.

В следующей таблице описаны различные сценарии SSPR для изменения пароля, сброса или списания на месте, и какие S-SUS предоставляют функцию.

| Компонент | Azure AD уровня "Бесплатный" | Office 365 бизнес премиум; | Microsoft 365 бизнес | Azure AD Premium (P1 или P2) |
| --- |:---:|:---:|:---:|:---:|
| **Изменение пароля только для облаков**<br />Когда пользователь Azure AD знает свой пароль и хочет изменить его на что-то новое. | ● | ● | ● | ● |
| **Сбросить пароля только для облаков**<br />Когда пользователь в Azure AD забыл свой пароль и должен сбросить его. | | ● | ● | ● |
| **Гибридное изменение пароля пользователя или сбросить с помощью обратного возврата на prem**<br />Когда пользователь Azure AD, синхронизированный с предприимчивым каталогом с помощью Azure AD Connect, хочет изменить или сбросить пароль, а также написать новый пароль обратно в prem. | | | ● | ● |

> [!WARNING]
> Планы лицензирования Standalone Office 365 не поддерживают SSPR с помощью списания. Эти планы лицензирования Office 365 требуют, чтобы эта функциональность работала в Azure AD Premium P1, Premium P2 или Microsoft 365 Business.

Дополнительную информацию о лицензировании, включая расходы, можно узнать на следующих страницах:

* [Цены на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Функции и возможности Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Мобильность предприятия и безопасность](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 корпоративный](https://www.microsoft.com/microsoft-365/enterprise).
* [Бизнес Microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Включение группового и пользовательского лицензирования

Azure AD поддерживает групповое лицензирование. Это позволяет администраторам назначать лицензии сразу группе пользователей, вместо назначения их по одной. Дополнительные сведения см. в разделе [Назначение лицензий группе пользователей в Azure Active Directory](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Некоторые службы Майкрософт доступны не во всех местах. Прежде чем лицензия может быть назначена пользователю, администратор должен указать свойство **местоположения использования** на пользователя. Назначение лицензий может осуществляться в разделе**Настройки** **профиля** >  **пользователя** > на портале Azure. *Если лицензии назначаются группам, все пользователи, для которых не указано расположение, наследуют расположение каталога.*

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать работу с SSPR, заполните следующее руководство:

> [!div class="nextstepaction"]
> [Учебник: Включить сбросить пароли самообслуживания (SSPR)](tutorial-enable-sspr.md)
