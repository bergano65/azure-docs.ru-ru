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
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917919"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Синхронизация Azure AD Connect: расширения каталогов
Расширения каталогов можно использовать для расширения схемы в Azure Active Directory (Azure AD) с помощью собственных атрибутов из локального каталога Active Directory. Эта функция позволяет создавать бизнес-приложения с помощью атрибутов, которыми вы по-прежнему можете управлять локально. Эти атрибуты могут быть использованы через [расширения.](https://docs.microsoft.com/graph/extensibility-overview
) Вы можете увидеть доступные атрибуты с помощью [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Вы также можете использовать эту функцию для создания динамических групп в Azure AD.

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

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Изменения конфигурации в Azure AD, внесенные мастером

Во время установки Azure AD Connect приложение регистрируется при условии наличия этих атрибутов. Это приложение отображается на портале Azure. Его имя всегда **Арендатор Схема расширение App**.

![Приложение расширения схемы](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Убедитесь, что вы **выбираете все приложения,** чтобы увидеть это приложение.

Атрибуты префиксированы **с расширением \_«ApplicationId».\_** ApplicationId имеет одинаковое значение для всех атрибутов в вашем аналоге Azure. Это значение понадобятся для всех других сценариев этой темы.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Просмотр атрибутов с помощью API графика Майкрософт

Эти атрибуты теперь доступны через Microsoft Graph API, с помощью [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> В API Microsoft Graphнеобходимо гостеву необходимо запросить возврат атрибутов. Явно выберите атрибуты, `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`как это: .
>
> Дополнительные сведения см. в разделе [Параметр select](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Использование атрибутов в динамических группах

Одним из наиболее полезных сценариев является использование этих атрибутов в группах динамической безопасности или Office 365.

1. Создайте новую группу в Azure AD. Дайте ему хорошее имя и убедитесь, что **тип членства** **является динамическим пользователем.**

   ![Скриншот с новой группой](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Выберите **для добавления динамического запроса.** Если вы посмотрите на свойства, то вы не увидите эти расширенные атрибуты. Вы должны добавить их в первую очередь. Нажмите **Нажмите Получить пользовательские свойства расширения,** введите идентификатор приложения, и нажмите **Обновления свойства**.

   ![Скриншот, где были добавлены расширения каталога](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Откройте выпадение свойств и обратите внимание, что добавленные атрибуты теперь видны.

   ![Скриншот с новыми атрибутами, отображаемыми в uI](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Заполните выражение в соответствии с вашими требованиями. В нашем примере правило устанавливается **на (user.extension_9d98ed114c4840d298fad781915f27e4_division - eq "Продажи и маркетинг")**.

4. После создания группы дайте Azure AD некоторое время, чтобы заполнить членов, а затем просмотреть членов.

   ![Скриншот с членами динамической группы](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](how-to-connect-sync-whatis.md) .

Подробнее об [интеграции личных данных с помощью Active Directory Azure Active.](whatis-hybrid-identity.md)
