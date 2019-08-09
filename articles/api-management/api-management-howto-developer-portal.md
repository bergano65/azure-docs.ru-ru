---
title: Доступ и настройка нового портала разработчика — управление API Azure | Документация Майкрософт
description: Узнайте, как использовать новый портал разработчика в оснастке управления API.
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
ms.openlocfilehash: 27d5dcc99db040036ba296911aa33d8a312bb23f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851513"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Доступ и настройка нового портала разработчика в службе управления API Azure

В этой статье показано, как получить доступ к новому порталу разработчика управления API Azure. В нем описывается добавление и редактирование содержимого, а также настройка внешнего вида веб-сайта.

![Новый портал разработчика для управления API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Предварительные требования

- Выполните инструкции из краткого руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Новый портал разработчика сейчас доступен в предварительной версии.

## <a name="managed-vs-self-hosted"></a>Управляемые и собственные версии

Создать портал разработчика можно двумя способами:

- **Управляемая версия** — изменение и Настройка портала, встроенного в экземпляр управления API и доступ к которому осуществляется через URL-адрес `<your-api-management-instance-name>.developer.azure-api.net`.
- **Самостоятельная версия** — путем развертывания и самостоятельного размещения портала за пределами экземпляра управления API. Такой подход позволяет изменять базу кода портала и расширять предоставляемые Основные функции. Дополнительные сведения и инструкции см. в [репозитории GitHub с исходным кодом портала][1].

## <a name="managed-access"></a>Доступ к управляемой версии портала

Чтобы получить доступ к управляемой версии портала, выполните следующие действия.

1. Перейдите к экземпляру службы управления API на портал Azure.
1. Щелкните новую кнопку **портала разработчика (Предварительная версия)** на верхней панели навигации. Откроется новая вкладка браузера с административной версией портала. При первом обращении к порталу содержимое по умолчанию будет автоматически подготовлено.

## <a name="managed-tutorial"></a>Изменение и Настройка управляемой версии портала

В видео ниже показано, как изменить содержимое портала, настроить внешний вид веб-сайта и опубликовать изменения.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Часто задаваемые вопросы

В этом разделе мы ответим на часто задаваемые вопросы о новом портале разработчика, который является общим характером. Вопросы, относящиеся к локальной версии, см. в [разделе Wiki репозитория GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Как перенести содержимое со старого портала разработчика на новый?

Вы не можете. Порталы несовместимы.

### <a name="when-will-the-portal-become-generally-available"></a>Когда портал станет общедоступным?

Сейчас портал находится в режиме предварительной версии, и он станет общедоступным в конце календарного года (2019). Предварительная версия не должна использоваться в рабочих целях.

### <a name="will-the-old-portal-be-deprecated"></a>Будет ли старый портал устаревшим?

Да, оно будет признано устаревшим после того, как новая версия станет общедоступной. Если у вас есть проблемы, их следует вызывать [в выделенной проблемы GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Есть ли у нового портала все функции старого портала?

Цель общей доступности заключается в том, чтобы обеспечить соответствие функций на основе сценариев старому порталу. До этого момента в предварительной версии могут не быть выбраны реализованные функции.

Исключениями являются *приложения* и *проблемы* со старого портала, которые не будут доступны на новом портале. Если вы используете *проблемы* на старом портале и потребуете их в новой, опубликуйте комментарий в [выделенной ошибке GitHub](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Я нашел ошибки и (или) хотел запросить функцию.

Отлично! Вы можете предоставить нам отзыв, отправить запрос на функцию или передать отчет об ошибке в [разделе проблем репозитория GitHub](https://github.com/Azure/api-management-developer-portal/issues). Мы также ценим ваши отзывы о проблемах, отмеченных `community` меткой.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Я хочу переместить содержимое нового портала между средами. Как это можно сделать и мне нужно использовать локальную версию?

Это можно сделать на портале, управляемом и размещенном в собственном расположении. Новый портал разработчика поддерживает извлечение содержимого с помощью API управления службы управления API. API-интерфейсы описаны [в разделе вики репозитория GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). Мы также написали [сценарий](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), который может помочь начать работу.

Мы по-прежнему работаем над обработкой этого процесса с помощью набора API DevOps Resource Kit.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Как выбрать *Макет* при создании новой *страницы*?

*Макет* применяется к странице путем сопоставления его шаблона URL-адреса со страницей . Например, *Макет* с `/wiki/*` шаблоном URL-адреса будет применяться ко `/wiki/` всем *страницам* с сегментом: `/wiki/getting-started`, `/wiki/styles`и т. д.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Почему Интерактивная консоль разработчиков не работает?

Скорее всего, это связано с CORS. Интерактивная консоль выполняет клиентский запрос API из браузера. Проблему CORS можно устранить, добавив [политику CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) в API-интерфейсы. Можно указать все параметры вручную (например, источник как https://contoso.com) или использовать подстановочное `*` значение).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о новом портале разработчика:

- [Репозиторий GitHub с исходным кодом][1]
- [Инструкции по самостоятельному размещению портала][2]
- [Общедоступный план проекта][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects