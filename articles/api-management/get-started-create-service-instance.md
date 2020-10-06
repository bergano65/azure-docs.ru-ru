---
title: Краткое руководство. Создание экземпляра службы "Управление API Azure"
description: Создание экземпляра службы "Управление API Azure" с помощью портала Azure.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708212"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Краткое руководство. Создание экземпляра службы "Управление API Azure" с помощью портала Azure

Служба управления API Azure помогает организациям публиковать API-интерфейсы для внешних пользователей, партнеров и собственных разработчиков, раскрывая таким образом потенциал своих данных и услуг. Служба управления API предоставляет базовые возможности для успешного выполнения программы API за счет привлечения разработчиков, бизнес-аналитики, анализа, безопасности и защищенности. Служба "Управление API" позволяет создавать современные шлюзы API для существующих серверных служб, размещенных в любом месте, и управлять ими. Дополнительные сведения см. в статье [Общие сведения о Службе контейнеров Azure](api-management-key-concepts.md).

В этом кратком руководстве описаны шаги по созданию экземпляра службы управления API с помощью портала Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Экземпляр управления API

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="create-a-new-service"></a>Создание службы

1. В меню портала Azure выберите **Создать ресурс**. Вы также можете выбрать команду **Cоздать ресурс** на **домашней странице** Azure. 
   
   Выберите :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Создать ресурс":::.

   
1. На странице **Создать** выберите **Интеграция** > **Управление API**.

   Новый экземпляр службы управления API Azure
   
1. На странице **Служба "Управление API"** введите настройки.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Создать ресурс" находится в сети, его можно использовать. Изучите сведения из учебника, чтобы [импортировать и опубликовать первый API](import-and-publish.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов и все связанные с ней ресурсы будут не нужны, их можно удалить. Для этого выполните следующие действия.

1. Войдите на портал Azure; найдите в поиске и выберите **Группы ресурсов**. Можно также выбрать **Группы ресурсов** на **домашней странице**. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Создать ресурс":::

1. На странице **Группы ресурсов** выберите свою группу ресурсов.

   Щелкните :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Создать ресурс":::.

1. На странице группы ресурсов выберите **Удалить группу ресурсов**. 
   
1. Введите имя группы ресурсов, которую необходимо удалить, и щелкните **Удалить**.

   Удаление группы ресурсов

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Импорт и публикация первого API](import-and-publish.md)
