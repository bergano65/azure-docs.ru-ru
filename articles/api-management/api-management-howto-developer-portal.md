---
title: Доступ и настройка нового портала разработчика - службе управления API Azure | Документация Майкрософт
description: Узнайте, как использовать новый портал разработчика в службе управления API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743580"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Доступ и настройка нового портала разработчика в службе управления API Azure

В этой статье показано, как перейти на новый портал разработчика управления API Azure. Он поможет вам выполнить визуальный редактор - Добавление и редактирование содержимого -, а также для настройки внешнего вида веб-сайта.

![Новый портал разработчика управления API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Технические условия

- Выполните инструкции из краткого руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> На новом портале разработчика в настоящее время доступна Предварительная версия.

## <a name="managed-and-self-hosted-versions"></a>Управляемые и локальной версии

Вы можете создать свой портал разработчика, двумя способами:

- **Управляемая версия** -, изменив и настройке портала встроены в экземпляре службы управления API и доступно через URL-адрес `<your-api-management-instance-name>.developer.azure-api.net`.
- **Локальная версия** — развертывание, размещение на собственном сервере портал за пределы экземпляра управления API. Такой подход позволяет изменить на портале базы кода и расширить предоставленный основные функциональные возможности. Сведения и инструкции см. в разделе [репозиторий GitHub с исходным кодом портала][1].

## <a name="access-the-managed-version-of-the-portal"></a>Доступ к управляемой версии портала

Выполните следующие действия, чтобы получить доступ к управляемой версии портала.

1. Перейдите к экземпляру службы управления API на портале Azure.
1. Щелкните **новый портал разработчика (Предварительная версия)** кнопку в верхней панели навигации. Откроется новая вкладка браузера с помощью административных версии портала. Если вы обращаетесь к порталу в первый раз, содержимое по умолчанию будет автоматически выполняться Подготовка.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Изменение и настройка на управляемую версию портала

В видео ниже показано, как внести правку в контент портала, настроить внешний вид веб сайта и публикации изменений.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о новом портале разработчика.

- [Репозиторий GitHub с исходным кодом][1]
- [Инструкции по порталу размещение на собственном сервере][2]
- [Дорожной карте проекта][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects