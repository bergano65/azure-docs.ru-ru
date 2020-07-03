---
title: 'Azure Active Directory: лицензирование самостоятельного сброса пароля'
description: Сведения о разнице Azure Active Directory требования к лицензированию самостоятельного сброса пароля
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393065"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Требования к лицензированию для самостоятельного сброса пароля Azure Active Directory

Чтобы снизить количество обращений в службу поддержки и потери производительности, когда пользователь не может войти на устройство или в приложение, учетные записи пользователей в Azure Active Directory (Azure AD) можно включить для самостоятельного сброса пароля (SSPR). Функции, составляющие SSPR, включают изменение пароля, сброс, разблокировку и обратную запись в локальный каталог. Базовые функции SSPR доступны для Office 365 и всех пользователей Azure AD без каких бы то ни было затрат.

В этой статье описаны различные способы лицензирования и использования самостоятельного сброса пароля. Конкретные сведения о ценах и выставлении счетов см. на [странице цен на Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Сравнение выпусков и функций

SSPR лицензируется для каждого пользователя. Чтобы обеспечить соответствие требованиям, организациям необходимо назначить соответствующую лицензию своим пользователям.

В следующей таблице описаны различные сценарии SSPR для изменения, сброса или обратной записи паролей, а также номера SKU, которые предоставляют эту функцию.

| Функция | Azure AD уровня "Бесплатный" | Office 365 бизнес премиум; | Microsoft 365 бизнес | Azure AD Premium (P1 или P2) |
| --- |:---:|:---:|:---:|:---:|
| **Изменение пароля пользователя только в облаке**<br />Когда пользователь в Azure AD знает свой пароль и хочет изменить его на что-то новое. | ● | ● | ● | ● |
| **Сброс пароля пользователя только в облаке**<br />Когда пользователь в Azure AD забыл свой пароль и его необходимо сбросить. | | ● | ● | ● |
| **Изменение или сброс пароля гибридного пользователя с помощью локальной обратной записи**<br />Когда пользователь в Azure AD, синхронизированный из локального каталога, использует Azure AD Connect хочет изменить или сбросить пароль, а также записать новый пароль обратно в локальную среду. | | | ● | ● |

> [!WARNING]
> Автономные планы лицензирования Office 365 не поддерживают SSPR с обратной записью в локальной среде. Для работы этих планов лицензирования Office 365 требуется Azure AD Premium P1, Premium P2 или Microsoft 365 бизнес, чтобы эти функции работали.

Дополнительные сведения о лицензировании, включая затраты, см. на следующих страницах:

* [Цены на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Функции и возможности Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 корпоративный](https://www.microsoft.com/microsoft-365/enterprise).
* [Microsoft 365 бизнес](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Включение группового и пользовательского лицензирования

Azure AD поддерживает лицензирование на основе групп. Это позволяет администраторам назначать лицензии сразу группе пользователей, вместо назначения их по одной. Дополнительные сведения см. в разделе [Назначение лицензий группе пользователей в Azure Active Directory](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Некоторые службы Майкрософт доступны не во всех расположениях. Прежде чем можно будет назначить лицензию пользователю, администратор должен указать для пользователя свойство " **место использования** ". Назначение лицензий можно выполнить в разделе "**Параметры** **профиля** >  **пользователя** > " в портал Azure. *Если лицензии назначаются группам, все пользователи, для которых не указано расположение, наследуют расположение каталога.*

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе с SSPR, выполните инструкции из следующего руководства.

> [!div class="nextstepaction"]
> [Учебник. Включение самостоятельного сброса пароля (SSPR)](tutorial-enable-sspr.md)
