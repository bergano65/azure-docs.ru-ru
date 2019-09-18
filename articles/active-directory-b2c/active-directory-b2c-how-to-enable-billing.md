---
title: Как связать подписку Azure с Azure Active Directory B2C | Документация Майкрософт
description: Пошаговое руководство по настройке выставления счетов для клиента Azure AD B2C в подписке Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 892f47b6acf22c62ce2290e2ede9d0bcd21eefc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065898"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Связывание подписки Azure с клиентом Azure Active Directory B2C

> [!IMPORTANT]
> Последние сведения о выставлении счетов на использование и ценах на Azure Active Directory B2C (Azure AD B2C) см. в разделе [цены на Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Счета за использование Azure AD B2C выставляются для подписки Azure. Администратор созданного клиента Azure AD B2C должен явным образом связать созданный клиент Azure AD B2C с подпиской Azure. В этой статье показано, как это сделать.

> [!NOTE]
> Подписку, связанную с клиентом Azure AD B2C, можно использовать только для выставления счетов за использование Azure AD B2C или других ресурсов Azure, включая дополнительные ресурсы Azure AD B2C.  Она не позволяет добавлять другие лицензируемые службы Azure или лицензии Office 365 в клиенте Azure AD B2C.

Чтобы реализовать такую связь, следует создать ресурс Azure AD B2C в целевой подписке Azure. В одной подписке Azure можно создать несколько клиентов B2C наряду с другими ресурсами Azure (такими как виртуальные машины, хранилища данных, приложения логики). Чтобы увидеть все ресурсы, размещенные в подписке, перейдите к клиенту Azure AD, с которым связана эта подписка.

Подписки поставщиков облачных решений (CSP) Azure поддерживаются в Azure AD B2C. Эта функция доступна при использовании API или портала Azure для Azure AD B2C и для всех ресурсов Azure. Администраторы подписок CSP могут связывать, перемещать и удалять связи с Azure AD B2C так же, как это делается для всех ресурсов Azure. Администрирование Azure AD B2C с помощью управления доступом на основе ролей не зависит от связи между клиентом Azure AD B2C и подпиской Azure CSP. Для управления доступом на основе ролей используются роли на основе клиента, а не на основе подписки.

Для работы с руководством требуется действующая подписка Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C

Прежде всего [создайте клиент Azure AD B2C](active-directory-b2c-get-started.md), который вы намерены связать с подпиской. Пропустите этот шаг, если у вас уже есть клиент Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Открытие портала Azure для клиента Azure AD с отображаемой подпиской Azure

Перейдите к клиенту Azure AD, в котором отображается нужная подписка Azure Откройте [Портал Azure](https://portal.azure.com) и перейдите к клиенту Azure AD, в котором отображается подписка Azure, которую вы намерены использовать.

![Переход к нужному клиенту Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Поиск Azure AD B2C в Azure Marketplace

Нажмите кнопку **Создать ресурс**. В поле **Поиск в Marketplace** введите `Active Directory B2C`.

![Снимок экрана портала с "Active Directory B2C" в поиске в Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

В списке результатов выберите **Azure AD B2C**.

![Служба Azure AD B2C, выбранная в списке результатов](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Отобразятся сведения об Azure AD B2C. Нажмите кнопку **Создать**, чтобы начать настройку нового клиента Azure Active Directory B2C.

В окне создания ресурса выберите **Связывание существующего B2C-клиента Azure AD с вашей подпиской Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Создание ресурса Azure AD B2C в подписке Azure

В диалоговом окне создания ресурса выберите в раскрывающемся списке клиент Azure AD B2C. Вы увидите полный список клиентов, для которых вы являетесь глобальным администратором и которые еще не связаны с подпиской.

Имя ресурса Azure AD B2C устанавливается автоматически и совпадает с доменным именем клиента Azure AD B2C.

В поле "Подписка" выберите активную подписку Azure, для которой вы являетесь администратором.

Выберите группу ресурсов и расположение группы ресурсов. Этот выбор не оказывает влияния на расположение, производительность и статус оплаты для этого клиента B2C.

![Страница создания Azure AD B2C ресурсов в портал Azure](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Управление ресурсами клиента Azure AD B2C

Когда создание ресурса Azure AD в подписке Azure успешно завершится, вы увидите новый ресурс типа "Клиент B2C" рядом с другими ресурсами Azure.

Этот ресурс можно использовать в следующих целях:

- для перехода к подписке, где можно просмотреть сведения о выставлении счетов;
- для перехода к клиенту Azure AD B2C;
- Отправить запрос на техническую поддержку.
- для перемещения ресурса "Клиент B2C" в другую подписку Azure или в другую группу ресурсов.

![Страница параметров ресурсов B2C в портал Azure](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Изменение подписки на выставление счетов клиента Azure AD B2C

Azure AD B2C клиенты могут быть перемещены в другую подписку, если исходная и Целевая подписки существуют в одном клиенте Azure Active Directory.

Чтобы узнать, как переместить ресурсы Azure, такие как клиент Azure AD B2C, в другую подписку, см. статью [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).

Прежде чем начать перемещение, обязательно прочитайте всю статью, чтобы полностью понять ограничения и требования для такого перемещения. Помимо инструкций по перемещению ресурсов, она включает в себя важную информацию, например контрольный список перед перемещением и проверку операции перемещения.

## <a name="known-issues"></a>Известные проблемы

### <a name="self-imposed-restrictions"></a>Самоназначенные ограничения

Пользователь может установить региональное ограничение на создание ресурсов Azure. Такие ограничения могут помешать созданию ресурса Azure AD B2C. Для устранения проблемы снимите это ограничение.

## <a name="next-steps"></a>Следующие шаги

Когда вы выполните эти шаги для каждого клиента Azure AD B2C, в выбранной подписке Azure начнется выставление счетов в соответствии с используемым соглашением Azure Direct или Enterprise.

Сведения об использовании и выставлении счетов вы можете просмотреть в выбранной подписке Azure. Также можно просмотреть подробные ежедневные отчеты об использовании с помощью [API отчетов об использовании](active-directory-b2c-reference-usage-reporting-api.md).
