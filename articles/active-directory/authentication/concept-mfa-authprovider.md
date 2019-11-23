---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: Когда следует использовать поставщик аутентификации с Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382006"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Когда следует использовать поставщик службы Многофакторной идентификации Azure

Двухфакторная проверка подлинности доступна по умолчанию для глобальных администраторов, у которых есть служба Azure Active Directory, и для пользователей Office 365. Однако если вы хотите использовать преимущества [дополнительных функций](howto-mfa-mfasettings.md), необходимо приобрести полную версию службы Многофакторной идентификации Microsoft Azure (MFA).

Поставщик Многофакторной идентификации Azure позволяет пользователям, у которых **нет лицензий**, воспользоваться преимуществами функций Многофакторной идентификации Azure.

> [!NOTE]
> С 1 сентября 2018 года создавать новые поставщики аутентификации невозможно. Existing auth providers may continue to be used and updated, but migration is no longer possible. Многофакторная аутентификация по-прежнему будет предоставляться как функция в лицензиях Azure AD ценовой категории "Премиум".

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Предупреждения о пакете SDK для Azure MFA

Обратите внимание на то, что этот пакет SDK является нерекомендуемым и будет работать только до 14 ноября 2018 года. После этого вызовы к пакету SDK будут завершаться сбоем.

## <a name="what-is-an-mfa-provider"></a>Что такое поставщик службы "Многофакторная идентификация"?

Поставщики аутентификации бывают двух типов. Их отличие заключается в способе оплаты за подписку Azure. При оплате за каждую проверку подлинности вычисляется количество событий проверки подлинности, выполненных для клиента в течение месяца. Этот вариант подойдет, если аутентификация редко выполняется для определенного числа пользователей. При оплате за каждого пользователя вычисляется количество пользователей в клиенте, выполнявших двухфакторную проверку подлинности в течение месяца. Этот вариант подойдет, если количества имеющихся лицензий для пользователей недостаточно для MFA.

## <a name="manage-your-mfa-provider"></a>Управление поставщиком службы "Многофакторная идентификация"

После создания поставщика MFA изменить модель использования невозможно (на включенного пользователя или на отдельную проверку подлинности).

Если вы приобрели достаточно лицензий, чтобы охватить всех пользователей, для которых включена MFA, можно полностью удалить поставщик MFA.

Если поставщик MFA не связан с клиентом Azure AD или новый поставщик MFA связан с другим клиентом Azure AD, пользовательские настройки и параметры конфигурации не будут применяться. Кроме того, вам потребуется повторно активировать существующие серверы Azure MFA с помощью учетных данных активации, созданных с помощью поставщика многофакторной идентификации. Повторная активация серверов MFA для их связи с поставщиком MFA не влияет на аутентификацию с помощью телефонного звонка и текстового сообщения в отличие от уведомлений мобильных приложений, которые перестанут работать, пока пользователи повторно не активируют мобильные приложения.

### <a name="removing-an-authentication-provider"></a>Removing an authentication provider

> [!CAUTION]
> There is no confirmation when deleting an authentication provider. Selecting **Delete** is a permanent process.

Authentication providers can be found in the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers**. Click on listed providers to see details and configurations associated with that provider.

Before removing an authentication provider, take note of any customized settings configured in your provider. Decide what settings need to be migrated to general MFA settings from your provider and complete the migration of those settings. 

Azure MFA Servers linked to providers will need to be reactivated using credentials generated under **Azure portal** > **Azure Active Directory** > **MFA** > **Server settings**. Before reactivating, the following files must be deleted from the `\Program Files\Multi-Factor Authentication Server\Data\` directory on Azure MFA Servers in your environment:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Delete an auth provider from the Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

When you have confirmed that all settings have been migrated, you can browse to the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers** and select the ellipses **...** and select **Delete**.

> [!WARNING]
> Deleting an authentication provider will delete any reporting information associated with that provider. You may want to save activity reports before deleting your provider.

> [!NOTE]
> Users with older versions of the Microsoft Authenticator app and Azure MFA Server may need to re-register their app.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка параметров Многофакторной идентификации](howto-mfa-mfasettings.md)
