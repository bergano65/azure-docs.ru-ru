---
title: Установка пакета содержимого Power BI для Azure AD | Документация Майкрософт
description: Узнайте, как установить пакет содержимого Power BI для Azure AD.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816656"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Краткое руководство. Установка пакета содержимого Power BI для Azure Active Directory

|  |
|--|
|В настоящее время пакет содержимого Azure AD Power BI использует API-интерфейсы Azure AD Graph для получения данных от вашего клиента Azure AD. Таким образом, можно заметить некоторые несоответствия между данными, доступными в пакете содержимого и данными, полученными с помощью [API-интерфейсов Microsoft Graph для создания отчетов](concept-reporting-api.md). |
|  |

Пакет содержимого Power BI для Azure Active Directory дает возможность получать полное представление о происходящем с Active Directory. Вы можете загрузить готовый пакет содержимого и с его помощью создавать отчеты о всех действиях в каталоге, используя разные средства визуализации Power BI. Кроме того, вы можете создавать собственные панели мониторинга и легко предоставлять к ним доступ любому пользователю в организации. 

В этом кратком руководстве описано, как установить этот пакет содержимого.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Учетная запись Power BI. Это та же учетная запись, которую вы используете с Office 365 или Azure AD. 
* Идентификатор клиента Azure AD. Это **идентификатор каталога**, указанный на [странице свойств](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) портала Azure.
* Лицензия Azure AD Premium (P1 или P2). 

## <a name="install-azure-ad-power-bi-content-pack"></a>Установка пакета содержимого Power BI для Azure AD 

1. Войдите в [Power BI](https://app.powerbi.com/groups/me/getdata/services), используя учетную запись этой службы. Это та же учетная запись, которую вы используете с Office 365 или Azure AD.

2. На странице **Приложения** выполните поиск по запросу **Журналы действий Azure Active Directory** и выберите **Получить**. 

   ![Пакет содержимого Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. Во всплывающем окне введите свой идентификатор клиента Azure AD, введите число **7** в поле количества дней, за которое нужно запросить данные, а затем выберите **Далее**.
    
   ![Пакет содержимого Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Когда панель мониторинга журналов действий Active Directory Azure будет создана, выберите ее.

   ![Пакет содержимого Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Дополнительная информация

* [Как использовать пакет содержимого Azure Active Directory Power BI](howto-power-bi-content-pack.md)
* [Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory](troubleshoot-content-pack.md)
