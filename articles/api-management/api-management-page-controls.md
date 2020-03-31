---
title: Элементы управления страницей в службе управления API Azure | Документация Майкрософт
description: Сведения об элементах управления страницей, доступных для использования в шаблонах портала разработчика в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244021"
---
# <a name="azure-api-management-page-controls"></a>Элементы управления страницей в службе управления API Azure
Служба управления API Azure предоставляет следующие элементы управления страницей для использования в шаблонах портала разработчика.  
  
Чтобы применить элемент управления, поместите его в нужное место шаблона портала разработчика. Некоторые элементы управления имеют параметры, как например [app-actions](#app-actions), представленный в следующем примере.  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Значения для этих параметров передаются в составе модели данных для шаблона. В большинстве случаев достаточно просто скопировать представленный пример для элемента управления, и он сразу будет правильно работать. Дополнительные сведения о значениях параметров вы найдете в разделе модели данных для каждого шаблона, в котором можно использовать соответствующий элемент управления.  

Дополнительные сведения о работе с шаблонами см. в статье [Настройка портала разработчика в службе управления API Azure с помощью шаблонов](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Элементы управления страницей в шаблонах портала разработчика  
  
-   [приложение-действия](#app-actions)  
-   [базовый-сизин](#basic-signin)  
-   [контроль над paging](#paging-control)  
-   [providers](#providers)  
-   [поиск-контроль](#search-control)  
-   [sign-up](#sign-up)  
-   [кнопка подписки](#subscribe-button)  
-   [отмена подписки](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 Элемент управления `app-actions` предоставляет пользовательский интерфейс для взаимодействия с приложениями на странице профиля пользователя на портале разработчика.  
  
 ![app&#45;управления действиями](./media/api-management-page-controls/APIM-app-actions-control.png "ApIM управление действиями приложения APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Параметры  
  
|Параметр|Описание|  
|---------------|-----------------|  
|appId|Идентификатор приложения.|  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `app-actions` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Приложения](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 Элемент управления `basic-signin` предоставляет интерфейс для сбора учетных данных пользователей на странице входа на портал разработчика.  
  
 ![основные&#45;контроль signin](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM базовый-сипин управления")  
  
### <a name="usage"></a>Использование  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `basic-signin` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Войти](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paging-control  
 Элемент `paging-control` предоставляет функцию разбиения по страницам, которую можно использовать на страницах со списками на портале разработчика.  
  
 ![контроль paging](./media/api-management-page-controls/APIM-paging-control.png "ApIM контроль paging")  
  
### <a name="usage"></a>Использование  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `paging-control` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Список API](api-management-api-templates.md#APIList)  
  
-   [Список проблем](api-management-issue-templates.md#IssueList)  
  
-   [Список продуктов](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Поставщиков  
 Элемент управления `providers` предоставляет интерфейс для выбора поставщика проверки подлинности на странице входа на портал разработчика.  
  
 ![контроль поставщиков](./media/api-management-page-controls/APIM-providers-control.png "Контроль поставщиков APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `providers` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Войти](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 Элемент `search-control` предоставляет функцию поиска, которую можно использовать на страницах со списками на портале разработчика.  
  
 ![Управление поиском](./media/api-management-page-controls/APIM-search-control.png "Контроль поиска APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `search-control` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Список API](api-management-api-templates.md#APIList)  
  
-   [Список продуктов](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Регистрация  
 Элемент управления `sign-up` предоставляет интерфейс для сбора информации о профиле пользователя на странице регистрации на портале разработчика.  
  
 ![подписать&#45;управление](./media/api-management-page-controls/APIM-sign-up-control.png "Контроль регистрации APIM")  
  
### <a name="usage"></a>Использование  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `sign-up` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Регистрация](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 Элемент `subscribe-button` предоставляет функцию оформления подписки пользователя на продукт.  
  
 ![подписаться&#45;кнопку управления](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM управление подпиской кнопки")  
  
### <a name="usage"></a>Использование  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Параметры  
 Нет.  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `subscribe-button` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Продукт](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 Элемент управления `subscription-cancel` предоставляет интерфейс для отмены подписки на продукт на странице профиля пользователя на портале разработчика.  
  
 ![подписка&#45;отменить контроль](./media/api-management-page-controls/APIM-subscription-cancel-control.png "ApIM контроль подписки-отмена")  
  
### <a name="usage"></a>Использование  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Параметры  
  
|Параметр|Описание|  
|---------------|-----------------|  
|subscriptionId|Идентификатор подписки для отмены.|  
|cancelUrl|URL-адрес для отмены подписки.|  
  
### <a name="developer-portal-templates"></a>Шаблоны портала разработчика  
 Элемент управления `subscription-cancel` можно использовать в следующих шаблонах портала разработчика:  
  
-   [Продукт](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о работе с шаблонами см. в статье [Настройка портала разработчика в службе управления API Azure с помощью шаблонов](api-management-developer-portal-templates.md).
