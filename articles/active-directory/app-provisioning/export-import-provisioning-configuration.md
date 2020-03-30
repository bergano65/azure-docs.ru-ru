---
title: Экспортируйте конфигурацию подготовки и откатитесь к известному хорошему состоянию для аварийного восстановления». Документы Майкрософт
description: Узнайте, как экспортировать конфигурацию подготовки и откатиться к известному хорошему состоянию для аварийного восстановления.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051310"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Экспорт конфигурации подготовки и откат в известное хорошее состояние

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Экспорт и импорт конфигурации подготовки с портала Azure

### <a name="how-can-i-export-my-provisioning-configuration"></a>Как экспортировать конфигурацию подготовки?
Для экспорта конфигурации:
1. На [портале Azure](https://portal.azure.com/) в области навигации слева выберите **Azure Active Directory**.
2. В панели **Active Directory Azure** выберите **приложения Enterprise** и выберите приложение.
3. В левом навигационном стежке выберите **подготовку.** Со страницы конфигурации подготовки нажмите на **отображение атрибутов,** затем **отобразите расширенные опции**и, наконец, **просмотрите свою схему.** Это приведет вас к редактору схемы. 
5. Нажмите на загрузку в панели команд в верхней части страницы, чтобы загрузить схему.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Аварийное восстановление - откат к известному хорошему состоянию
Экспорт и сохранение конфигурации позволяет откатить к предыдущей версии конфигурации. Мы рекомендуем экспортировать конфигурацию подготовки и сохранить ее для последующего использования в любое время, когда вы внесете изменения в отображение атрибутов или фильтры для поиска. Все, что вам нужно сделать, это открыть файл JSON, который вы загрузили в шагах выше, скопировать все содержимое файла JSON, заменить все содержимое полезной нагрузки JSON в редакторе схемы, а затем сохранить. При наличии активного цикла подготовки он завершится и в следующем цикле будет использоваться обновленная схема. Следующий цикл также будет первоначальным циклом, который переоценивает каждого пользователя и группу на основе новой конфигурации. Рассмотрим следующее при откате к предыдущей конфигурации:
* Пользователи будут оценены еще раз, чтобы определить, если они должны быть в области. Если фильтры проверки изменились, пользователь больше не находится в сфере, он будет отключен. Хотя это желаемое поведение в большинстве случаев, Есть моменты, когда вы можете предотвратить это и может использовать [пропустить из сферы удаления](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) функциональности. 
* Изменение конфигурации подготовки перезапускает службу и запускает [начальный цикл.](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Экспорт и импортировать конфигурацию подготовки с помощью API Microsoft Graph
Можно использовать API Microsoft Graph и Microsoft Graph Explorer для экспорта отображения атрибутов и схемы подготовки пользователей в файл JSON и импорта его обратно в Azure AD. Вы также можете использовать захваченные здесь шаги для создания резервного копирования конфигурации подготовки. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Шаг 1: Получить ваш основной идентификатор службы предоставления приложений (Object ID)

1. Запустите [портал Azure](https://portal.azure.com)и перейдите к разделу Свойств вашего приложения подготовки. Например, если вы хотите экспортировать рабочий день в приложение *AD-поставщик,* отображая отображение, перейдите в раздел Свойства этого приложения. 
1. В разделе "Свойства" вашего приложения подготовки скопируйте значение GUID, связанное с полем *Идентификатор объекта*. Это значение также называется **ServicePrincipalId** вашего приложения и будет использоваться в операциях Microsoft Graph Explorer.

   ![Идентификатор субъекта-службы приложения Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Шаг 2: Войти в Microsoft Graph Explorer

1. Запустите [обозреватель Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Нажмите кнопку "Вход с помощью учетной записи Майкрософт" и войдите, используя учетные данные глобального администратора Azure AD или администратора приложения.

    ![Microsoft Граф Войти в](./media/export-import-provisioning-configuration/wd_export_02.png)

1. После выполнения входа вы увидите данные учетной записи пользователя на левой панели.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Шаг 3: Получить идентификатор вакансии для получения

В обозревателе Microsoft Graph выполните следующий запрос GET, заменив [servicePrincipalId] на атрибут **ServicePrincipalId**, извлеченный на [шаге 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Вы получите ответ, похожий на показанный ниже. Скопируйте атрибут идентификатора из ответа. Это значение представляет собой **ProvisioningJobId** и будет использоваться для получения метаданных базовой схемы.

   [![Предоставление идентификатора вакансии](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Шаг 4: Скачать схему подготовки

В обозревателе Microsoft Graph выполните следующий запрос GET, заменив [servicePrincipalId] и [ProvisioningJobId] на значения ServicePrincipalId и ProvisioningJobId, полученные на предыдущих шагах.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Скопируйте объект JSON из ответа и сохраните его в файл, чтобы создать резервную копию схемы.

### <a name="step-5-import-the-provisioning-schema"></a>Шаг 5: Импорт схемы обеспечения

> [!CAUTION]
> Выполните этот шаг только в том случае, если необходимо изменить схему конфигурации, которую нельзя изменить с помощью портала Azure, или если требуется восстановить конфигурацию из ранее сохраненной резервной копии с допустимой и рабочей схемой.

В обозревателе Microsoft Graph настройте следующий запрос PUT, заменив [servicePrincipalId] и [ProvisioningJobId] на значения ServicePrincipalId и ProvisioningJobId, полученные на предыдущих шагах.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

На вкладке текста запроса скопируйте содержимое файла схемы JSON.

   [![Запрос тела](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

На вкладке заголовков запроса добавьте атрибут заголовка Content-Type со значением "application/json".

   [![Запрос заголовков](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Нажмите кнопку "Выполнить запрос", чтобы импортировать новую схему.
