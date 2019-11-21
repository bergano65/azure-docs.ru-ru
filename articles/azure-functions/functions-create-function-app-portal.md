---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230772"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Создание приложения-функции на портале Azure

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Приложение-функция — это контейнер, в котором выполняются отдельные функции. 

## <a name="create-a-function-app"></a>Создание приложения-функции

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

При создании приложения-функции укажите допустимое **имя приложения**, содержащее только буквы, цифры и дефисы. Символ подчеркивания ( **_** ) использовать нельзя.

Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв. Имя учетной записи хранения должно быть уникальным в Azure. 

После создания приложения-функции можно создать отдельные функции на одном или нескольких языках программирования. Создайте функции [с помощью портала](functions-create-first-azure-function.md#create-function), [непрерывного развертывания](functions-continuous-deployment.md) или [передачи по FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Планы обслуживания

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Дополнительные сведения см. в разделе [Выбор правильного плана обслуживания для Функций Azure](functions-scale.md).

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Дополнительные сведения см. в разделе [обзора функций для JavaScript](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Требования к учетной записи хранения

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Для внутренних операций, таких как управление триггерами и ведение журнала выполнения функций, Функции Azure используют службу хранилища. Некоторые учетные записи хранения не поддерживают очереди и таблицы. Например, это относится к учетным записям хранения только для больших двоичных объектов, службе хранилища Azure уровня "Премиум" и учетным записям хранения общего назначения с репликацией ZRS. Эти учетные записи будут отфильтрованы из колонки "Учетная запись хранения" при создании приложения-функции.

>[!NOTE]
>При использовании плана потребления файлы кода и конфигурации привязок приложения-функции хранятся в хранилище файлов Azure в основной учетной записи хранения. При удалении основной учетной записи хранения это содержимое удаляется без возможности восстановления.

Дополнительные сведения о типах учетных записей хранения см. в разделе [Введение в службы хранилища Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Дальнейшие действия

Хотя портал Azure позволяет легко создать и опробовать функции, мы рекомендуем [локальную разработку](functions-develop-local.md). После создания приложения-функции на портале необходимо еще добавить функцию. 

> [!div class="nextstepaction"]
> [Добавление функции, активируемой HTTP](functions-create-first-azure-function.md#create-function)
