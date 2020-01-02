---
title: 'Синхронизация Azure AD Connect: расширения каталогов | Документация Майкрософт'
description: В этой статье описывается функция расширений каталогов в Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 138ca9bf3352c46b8ac495b58a2fd6d7bafeb658
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889863"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Синхронизация Azure AD Connect: расширения каталогов
Расширения каталогов можно использовать для расширения схемы в Azure Active Directory (Azure AD) с помощью собственных атрибутов из локального каталога Active Directory. Эта функция позволяет создавать бизнес-приложения с помощью атрибутов, которыми вы по-прежнему можете управлять локально. Эти атрибуты могут быть использованы через [расширения каталогов API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) или [Microsoft Graph](https://developer.microsoft.com/graph/). Просмотреть доступные атрибуты можно с помощью [проводника Azure AD Graph](https://graphexplorer.azurewebsites.net/) и [проводника Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) соответственно. Эту функцию также можно использовать для создания динамических групп в Azure AD.

В настоящее время рабочие нагрузки Office 365 не используют эти атрибуты.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Настройка атрибутов для синхронизации с Azure AD

Дополнительные атрибуты для синхронизации выбираются в разделе пользовательских параметров мастера установки.

>[!NOTE]
>Значения в поле доступных атрибутов следует вводить с учетом регистра.

![Мастер расширения схемы](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

При установке отображаются следующие допустимые атрибуты:

* Типы объектов пользователей и групп
* Однозначные атрибуты: строка, логическое значение, целое число, двоичное значение.
* Многозначные атрибуты: строка, двоичное значение.


>[!NOTE]
> Azure AD Connect поддерживает синхронизацию многозначных атрибутов Active Directory с Azure AD в качестве многозначных расширений каталогов. Но в настоящее время не существует способа извлечения и использования данных, отправленных в атрибуты многозначных расширений каталогов.

Список атрибутов считывается из кэша схемы, созданного во время установки Azure AD Connect. Если в схему Active Directory добавлены дополнительные атрибуты, они будут отображаться только после [обновления схемы](how-to-connect-installation-wizard.md#refresh-directory-schema).

Объект в Azure AD может иметь до 100 атрибутов расширений каталога. Максимальная длина составляет 250 символов. Если значение атрибута больше, он будет усечен модулем синхронизации.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Изменения конфигурации в Azure AD, сделанные мастером

Во время установки Azure AD Connect приложение регистрируется при условии наличия этих атрибутов. Это приложение отображается на портале Azure. Его имя всегда является **приложением расширения схемы клиента**.

![Приложение расширения схемы](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Убедитесь, что выбраны **все приложения** для просмотра этого приложения.

Атрибуты имеют префикс **расширения \_{applicationId}\_** . ApplicationId имеет одинаковое значение для всех атрибутов в клиенте Azure AD. Это значение потребуется для всех других сценариев в этом разделе.

## <a name="viewing-attributes-using-graph"></a>Просмотр атрибутов с помощью Graph

Теперь эти атрибуты доступны с помощью API Azure AD Graph. Их можно запросить через [обозреватель Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Обозреватель Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Или же вы можете запросить атрибуты через API Microsoft Graph с помощью [обозревателя Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> В Microsoft Graph необходимо запросить возврат атрибутов. Явно выберите такие атрибуты: HTTPS\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division.
>
> Дополнительные сведения см. в разделе [Параметр select](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Использование атрибутов в динамических группах

Одним из наиболее полезных сценариев является использование этих атрибутов в группах Dynamic Security или Office 365.

1. Создайте новую группу в Azure AD. Присвойте ему хорошее имя и убедитесь, что **Тип членства** является **динамическим пользователем**.

   ![Снимок экрана с новой группой](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Выберите, чтобы **добавить динамический запрос**. Если взглянуть на свойства, эти расширенные атрибуты отображаться не будут. Их необходимо добавить первыми. Щелкните **получить пользовательские свойства расширения**, введите идентификатор приложения и нажмите кнопку **обновить свойства**.

   ![Снимок экрана с добавленными расширениями каталогов](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Откройте раскрывающийся список свойств и обратите внимание, что добавленные атрибуты теперь видимы.

   ![Снимок экрана с новыми атрибутами, отображаемыми в пользовательском интерфейсе](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Заполните выражение в соответствии с вашими требованиями. В нашем примере правило имеет значение **(User. extension_9d98ed114c4840d298fad781915f27e4_division-EQ «Sales and Marketing»)** .

4. После создания группы предоставьте Azure AD некоторое время для заполнения участников, а затем просмотрите участников.

   ![Снимок экрана с элементами в динамической группе](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](how-to-connect-sync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).
