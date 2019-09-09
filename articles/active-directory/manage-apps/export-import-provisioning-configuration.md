---
title: Экспорт или импорт конфигурации подготовки с помощью API Graph | Документация Майкрософт
description: Узнайте, как экспортировать и импортировать конфигурацию подготовки с помощью API Graph.
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
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815824"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Экспортируйте или импортируйте конфигурацию подготовки с помощью API Graph

Вы можете использовать Microsoft Graph API и обозреватель графа для экспорта сопоставлений атрибутов подготовки пользователей и схемы в JSON-файл и импортировать их обратно в Azure AD. Вы также можете использовать описанные здесь действия, чтобы создать резервную копию конфигурации подготовки. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Шаг 1. Получение идентификатора субъекта-службы приложения для подготовки (идентификатор объекта)

1. Запустите [портал Azure](https://portal.azure.com)и перейдите к разделу Properties приложения подготовки. Например, если вы хотите экспортировать *приложение "Workday в службу подготовки пользователей* ", перейдите в раздел свойств этого приложения. 
1. В разделе "Свойства" вашего приложения подготовки скопируйте значение GUID, связанное с полем *Идентификатор объекта*. Это значение также называется идентификатором **ServicePrincipalId** вашего приложения, и оно будет использоваться в операциях обозревателя Graph.

   ![Идентификатор субъекта-службы приложения Workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Шаг 2. Выполнение входа в обозреватель Microsoft Graph

1. Запустите [обозреватель Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Нажмите кнопку "Вход с помощью учетной записи Майкрософт" и войдите, используя учетные данные глобального администратора Azure AD или администратора приложения.

    ![Выполнение входа в Graph](./media/export-import-provisioning-mappings/wd_export_02.png)

1. После выполнения входа вы увидите данные учетной записи пользователя на левой панели.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Шаг 3. Получение идентификатора задания подготовки для приложения подготовки

В обозревателе Microsoft Graph выполните следующий запрос GET, заменив [servicePrincipalId] на атрибут **ServicePrincipalId**, извлеченный на [шаге 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Вы получите ответ, похожий на показанный ниже. Скопируйте атрибут идентификатора из ответа. Это значение представляет собой **ProvisioningJobId** и будет использоваться для получения метаданных базовой схемы.

   [![Идентификатор задания подготовки](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Шаг 4. Скачивание схемы подготовки

В обозревателе Microsoft Graph выполните следующий запрос GET, заменив [servicePrincipalId] и [ProvisioningJobId] на значения ServicePrincipalId и ProvisioningJobId, полученные на предыдущих шагах.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Скопируйте объект JSON из ответа и сохраните его в файл, чтобы создать резервную копию схемы.

## <a name="step-5-import-the-provisioning-schema"></a>Шаг 5. Импорт схемы подготовки

> [!CAUTION]
> Выполните этот шаг только в том случае, если необходимо изменить схему конфигурации, которую нельзя изменить с помощью портала Azure, или если требуется восстановить конфигурацию из ранее сохраненной резервной копии с допустимой и рабочей схемой.

В обозревателе Microsoft Graph настройте следующий запрос PUT, заменив [servicePrincipalId] и [ProvisioningJobId] на значения ServicePrincipalId и ProvisioningJobId, полученные на предыдущих шагах.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

На вкладке текста запроса скопируйте содержимое файла схемы JSON.

   [![Текст запроса](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

На вкладке заголовков запроса добавьте атрибут заголовка Content-Type со значением "application/json".

   [![Заголовки запроса](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Нажмите кнопку "Выполнить запрос", чтобы импортировать новую схему.
