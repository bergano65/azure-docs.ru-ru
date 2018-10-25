---
title: 'Синхронизация Azure AD Connect: расширения каталогов | Документация Майкрософт'
description: В этой статье описывается функция расширений каталогов в Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9c40cfd9b15f2ae6398b70effc4f4d38111cfe1e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886071"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Синхронизация Azure AD Connect: расширения каталогов
Расширения каталогов можно использовать для расширения схемы в Azure Active Directory (Azure AD) с помощью собственных атрибутов из локального каталога Active Directory. Эта функция позволяет создавать бизнес-приложения с помощью атрибутов, которыми вы по-прежнему можете управлять локально. Эти атрибуты могут быть использованы через [расширения каталогов API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) или [Microsoft Graph](https://graph.microsoft.io/). Просмотреть доступные атрибуты можно с помощью [проводника Azure AD Graph](https://graphexplorer.azurewebsites.net/) и [проводника Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) соответственно.

В настоящее время рабочие нагрузки Office 365 не используют эти атрибуты.

Дополнительные атрибуты для синхронизации выбираются в разделе пользовательских параметров мастера установки.

>[!NOTE]
>Значения в поле доступных атрибутов следует вводить с учетом регистра.

![Мастер расширения схемы](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

При установке отображаются следующие допустимые атрибуты:

* Типы объектов пользователей и групп
* Однозначные атрибуты: строка, логическое значение, целое число, двоичное значение.
* Многозначные атрибуты: строка, двоичное значение.


>[!NOTE]
> Azure AD Connect поддерживает синхронизацию многозначных атрибутов Active Directory с Azure AD в качестве многозначных расширений каталогов. Но в настоящее время в Azure AD не предусмотрены функции, которые поддерживают многозначные расширения каталогов.

Список атрибутов считывается из кэша схемы, созданного во время установки Azure AD Connect. Если в схему Active Directory добавлены дополнительные атрибуты, они будут отображаться только после [обновления схемы](how-to-connect-installation-wizard.md#refresh-directory-schema).

Объект в Azure AD может иметь до 100 атрибутов расширений каталога. Максимальная длина составляет 250 символов. Если значение атрибута больше, он будет усечен модулем синхронизации.

Во время установки Azure AD Connect приложение регистрируется при условии наличия этих атрибутов. Это приложение отображается на портале Azure.

![Приложение расширения схемы](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Атрибуты имеют префикс extension\_{ИД_клиента_приложения}\_. AppClientId имеет одно значение для всех атрибутов в вашем клиенте Azure AD.

Теперь эти атрибуты доступны с помощью API Azure AD Graph. Их можно запросить через [обозреватель Azure AD Graph](https://graphexplorer.azurewebsites.net/).

![Обозреватель Azure AD Graph](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Или же вы можете запросить атрибуты через API Microsoft Graph с помощью [обозревателя Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Необходимо запросить возврат атрибута. Явно выберите такие атрибуты, как https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Дополнительные сведения см. в разделе [Параметр select](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о настройке [службы синхронизации Azure AD Connect](how-to-connect-sync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).
