---
title: Экспорт API из службы "Управление API Azure" в Power Platform | Документация Майкрософт
description: Узнайте, как экспортировать API из службы "Управление API" в Power Platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82702655"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Экспорт API из службы "Управление API" в Power Platform 

Разработчикам, использующим Microsoft [Power Platform](https://powerplatform.microsoft.com), часто нужны бизнес-возможности, созданные профессиональными разработчиками и развернутые в Azure. [Служба "Управление API Azure"](https://aka.ms/apimrocks) позволяет профессиональным разработчикам публиковать внутренние службы в качестве API и без усилий экспортировать эти API в Power Platform (Power Apps и Power Automate) в качестве настраиваемых соединителей для использования другими разработчиками. 

В этой статье показано, как экспортировать API из службы "Управление API" в Power Platform. 

## <a name="prerequisites"></a>Предварительные требования

+ Выполните инструкции из краткого руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md)
+ Убедитесь, что в экземпляре службы "Управление API" есть API, который вы хотите экспортировать в Power Platform.
+ Убедитесь, что у вас есть [среда](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) Power Apps или Power Automate. 

## <a name="export-an-api"></a>Экспорт API

1. На портале Azure перейдите к службе "Управление API" и выберите **Интерфейсы API** в меню.
2. Щелкните три точки рядом с API, который необходимо экспортировать. 
3. Щелкните **Экспортировать**.
4. Выберите **Power Apps и Power Automate**.
5. Выберите среду, в которую нужно экспортировать API. 
6. Укажите отображаемое имя, которое будет использоваться в качестве имени настраиваемого соединителя.  
7. Если API защищен сервером OAuth 2.0, необходимо предоставить дополнительные сведения, включая `Client ID`, `Client secret`, `Authorization URL`, `Token URL` и `Refresh URL`.  
8. Щелкните **Экспортировать**. 

Когда экспорт будет завершен, перейдите в среду Power App или Power Automate. Вы увидите API в виде настраиваемого соединителя.

## <a name="next-steps"></a>Дальнейшие действия

* [Изучите возможности Power Platform](https://powerplatform.microsoft.com/)
* [Выполните типичные задачи в службе "Управление API" с помощью доступных руководств](https://docs.microsoft.com/azure/api-management/import-and-publish)