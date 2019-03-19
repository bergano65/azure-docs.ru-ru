---
title: Учетные записи хранения в Azure Stack | Документация Майкрософт
description: Узнайте, как создать учетную запись хранения Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/18/2019
ms.author: mabrigg
ms.lastreviewed: 1/18/2019
ms.openlocfilehash: 1d980ec1cac0ecf095adecd4eb6f22f7765ce11f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779137"
---
# <a name="storage-accounts-in-azure-stack"></a>Учетные записи хранения в Azure Stack
Учетные записи хранения включают в себя службы BLOB-объектов и таблиц, а также уникальное пространство имен для объектов данных хранилища. По умолчанию данные в учетной записи доступны только владельцу учетной записи хранения.

1. На компьютере подтверждения концепции Azure Stack войдите в `https://adminportal.local.azurestack.external` в качестве [администратора](azure-stack-connect-azure-stack.md), а затем щелкните **+ Create a resource** (+ Создать ресурс)  > **Данные+хранилище** > **Учетная запись хранения**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. В колонке **Создание учетной записи хранения** введите имя учетной записи хранения. Создайте **группу ресурсов** или выберите имеющуюся, а затем щелкните **Создать** для создания учетной записи хранения.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Чтобы просмотреть новую учетную запись хранения, щелкните **Все ресурсы**, а затем найдите учетную запись хранения и щелкните ее имя.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Дополнительная информация
[Использование шаблонов диспетчера ресурсов Azure](user/azure-stack-arm-templates.md)

[Узнайте об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md)

[Скачайте руководство по проверке согласованного с Azure хранилища Azure Stack](https://aka.ms/azurestacktp1doc)
