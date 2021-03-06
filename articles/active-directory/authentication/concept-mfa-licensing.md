---
title: Версии Azure MFA и планы потребления — Azure Active Directory
description: Информация о клиенте многофакторной идентификации и ее доступных методах и версиях.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61a2afad33aea7fa2a21135dca0995ad862a2258
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848669"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Как получить службу Многофакторной идентификации Azure

Когда речь идет о защите учетных записей, двухшаговая проверка подлинности должна быть общепринятым стандартом в организации. Эта функция особенно важна для учетных записей, которые имеют привилегированный доступ к ресурсам. Поэтому корпорация Майкрософт поддерживает основные возможности двухфакторной проверки подлинности для администраторов Office 365 и Azure Active Directory (Azure AD) без дополнительной оплаты. Если вы хотите расширить эти возможности для администраторов или включить двухфакторную проверку подлинности для остальных пользователей, вы можете приобрести службу Многофакторной идентификации Azure несколькими способами.

> [!IMPORTANT]
> Эта статья — руководство, которое поможет вам освоить разные способы приобретения службы Многофакторной идентификации Azure. Сведения о ценах и выставлении счетов всегда можно найти [на странице цен на Многофакторную идентификацию](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Доступные версии службы Многофакторной идентификации Azure

В следующей таблице описаны различия между версиями многофакторной проверки подлинности.

| Версия | Описание |
| --- | --- |
| Бесплатный вариант | Клиенты, которые используют бесплатные преимущества Azure AD, могут использовать [Параметры безопасности по умолчанию](../conditional-access/concept-conditional-access-security-defaults.md) для включения многофакторной проверки подлинности в своей среде. |
| Многофакторная идентификация для Office 365 | Эта версия управляется на портале Office 365 или Microsoft 365. Администраторы могут [защитить свои ресурсы Office 365 с помощью двухшаговой проверки подлинности](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Эта версия является частью подписки Office 365. |
| Многофакторная идентификация для администраторов Azure AD | Пользователи, которым назначена роль глобального администратора Azure AD в клиентах Azure AD, могут включить двухфакторную проверку подлинности без дополнительных затрат. |
| Многофакторная идентификация Azure | Служба Многофакторной идентификации Azure, также называемая полной версией, предлагает широчайший набор возможностей. Доступны дополнительные варианты настройки с помощью [портала Azure](https://portal.azure.com), расширенные возможности отчетности и поддержка большого количества локальных и облачных приложений. Многофакторная идентификация Azure — это функция [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) и [Microsoft 365 бизнес](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> С 1 сентября 2018 года новые клиенты больше не могут приобрести Многофакторную идентификацию Azure как отдельное предложение. Многофакторная идентификация будет по-прежнему доступна в качестве компонента в Azure AD Premium или Microsoft 365 бизнес лицензий.

## <a name="feature-comparison-of-versions"></a>Сравнение функций в разных версиях

Приведенная ниже таблица содержит список функций, доступных в различных версиях Многофакторной идентификации Azure.

> [!NOTE]
> В этой сравнительной таблице рассматриваются функции, которые являются частью каждой версии Многофакторной идентификации. При наличии полной службы Многофакторной идентификации Azure некоторые возможности могут быть недоступны в зависимости от того, используется ли [MFA в облаке или MFA в локальной системе](concept-mfa-whichversion.md).
>

| Компонент | Многофакторная идентификация для Office 365 | Многофакторная идентификация для администраторов Azure AD | Многофакторная идентификация Azure | Параметры безопасности по умолчанию | 
| --- |:---:|:---:|:---:|:---:|
| Защита учетных записей администраторов Azure AD с помощью MFA |● |● (только учетные записи глобального администратора Azure AD) |● |● |
| Мобильное приложение в качестве второго фактора |● |● |● |● |
| Телефонный вызов в качестве второго фактора |● |● |● |   |
| SMS в качестве второго фактора |● |● |● |   |
| Пароли приложений для клиентов, которые не поддерживают Multi-Factor Authentication |● |● |● |   |
| Администраторский контроль над методами проверки |● |● |● |   |
| Защита пользовательских учетных записей с помощью MFA |● | |● |● |
| Режим ПИН-кода | | |● |   |
| Оповещение о мошенничестве | | |● |   |
| Отчеты службы "Многофакторная идентификация Azure" | | |● |   |
| Одноразовый обход проверки | | |● |   |
| Настраиваемые приветствия для телефонных вызовов | | |● |   |
| Настраиваемый идентификатор вызывающей стороны для телефонных звонков | | |● |   |
| Надежные IP-адреса | | |● |   |
| Запоминание данных MFA для доверенных устройств |● |● |● |   |
| MFA для локальных приложений | | |● |   |

> [!IMPORTANT]
> Начиная с марта 2019 параметры телефонного звонка не будут доступны для пользователей MFA и SSPR в бесплатных или пробных клиентах Azure AD. Это изменение не влияет на SMS messages. Телефонный звонок будет по прежнему доступен пользователям в платных клиентах Azure AD. Это изменение касается только бесплатных и пробных клиентов Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Как включить многофакторную проверку подлинности Azure для администраторов Azure

Пользователь, которому назначена роль глобального администратора клиентов Azure AD, может включить двухфакторную проверку подлинности для своих учетных записей глобального администратора Azure AD без дополнительных затрат. Если вы используете учетную запись Майкрософт, ее можно зарегистрировать для многофакторной проверки подлинности, выполнив инструкции в статье о поддержке учетных записей Майкрософт, [Сведения о двухфакторной проверке подлинности](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Если вы не используете учетную запись Майкрософт, включите многофакторную проверку подлинности для глобальных администраторов, следуя инструкциям в статье [Как настроить требование двухфакторной проверки подлинности пользователя или группы](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Способы приобретения Многофакторной идентификации Azure

Приобретите лицензии, включающие многофакторную идентификацию Azure, например Azure Active Directory Premium или пакет лицензий, который включает Azure AD Premium или условный доступ, и назначьте их пользователям в Azure Active Directory.

### <a name="consumption-based-licensing"></a>Лицензирование на основе потребления

Лицензирование на основе потребления больше не доступно для новых клиентов, действующих с 1 сентября 2018 г.

Начиная с 1 сентября 2018 новые поставщики проверки подлинности могут больше не создаваться. Существующие поставщики аутентификации можно продолжать использовать и обновлять. Многофакторная аутентификация по-прежнему будет предоставляться как функция в лицензиях Azure AD Premium.

При использовании поставщика Многофакторной идентификации Azure доступны две модели использования, которые тарифицируются по подписке Azure:

1. **На включенного пользователя**. Этот вариант предприятия обычно выбирают, чтобы применить двухшаговую проверку для известного числа определенных сотрудников, которым регулярно требуется выполнять аутентификацию. Оплата в этом варианте взимается в зависимости от количества пользователей, для которых активирована служба MFA в клиенте Microsoft Azure AD и на сервере Azure MFA. Если служба MFA включена для пользователей как в Azure AD, так и на сервере Azure MFA, и при этом включена синхронизация домена (Azure AD Connect), то учитывается та система, в которой пользователей MFA больше. Если синхронизация домена не используется, то учитывается сумма всех пользователей, для которых включена MFA — в Azure AD и на сервере Azure MFA. Сумма к оплате вычисляется пропорционально и передается в коммерческую систему ежедневно.

   > [!NOTE]
   > Выставление счетов: пример 1. На сегодня MFA в вашей организации включена для 5000 пользователей. Система MFA делит это значение на 31 и фиксирует на этот день оплату за 161,29 пользователей. На следующий день вы добавляете еще 15 пользователей, и теперь система MFA фиксирует 161,77 пользователей за день. В конце цикла выставления счетов количество пользователей, за которые выставлены счета в вашей подписке Azure, составит в сумме примерно 5000.
   >
   > Выставление счетов: пример 2. У вас есть определенное число пользователей с лицензиями. Для остальных вы используете поставщика MFA Azure с оплатой за количество пользователей. Для вашего клиента приобретено 4500 лицензий Enterprise Mobility + Security, а многофакторная идентификация включена для 5000 пользователей. В подписке Azure выставляется счет за 500 пользователей, что в пропорциональном распределении составляет 16,13 пользователя в день.
   >

1. **На аутентификацию подлинности**. Этот вариант предприятия обычно выбирают, чтобы использовать двухшаговую проверку подлинности для больших групп пользователей, которым выполнять аутентификацию нужно нечасто. Выставление счетов основано на количестве запросов на двухфакторную проверку подлинности, независимо от успешности выполнения этих проверок. Счета по этой позиции появляются в отчетах по использованию Azure пакетами по 10 проверок ежедневно.

   > [!NOTE]
   > Выставление счетов: пример 3. На сегодня служба Azure MFA получила 3105 запросов на двухшаговую проверку. В подписке Azure выставляется счет за 310,5 пакетов аутентификации.
   >

Важно отметить, что даже при наличии лицензий вы можете получать счета за конфигурацию с оплатой по мере использования. Если вы настроили поставщик Azure MFA для проверки подлинности, Вам выставляется счет за каждый запрос на двухфакторную проверку, даже на запросы пользователей, имеющих лицензии. Если вы настроили поставщик Azure MFA в домене, который не связан с вашим клиентом Azure AD, вы оплачиваете каждого включенного в нем пользователя, даже если у них есть лицензии в Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о ценах [на многофакторную проверку подлинности Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)
