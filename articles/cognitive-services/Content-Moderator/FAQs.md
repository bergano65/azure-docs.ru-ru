---
title: Часто задаваемые вопросы — Content Moderator
titlesuffix: Azure Cognitive Services
description: Ознакомьтесь с ответами на часто задаваемые вопросы о Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268927"
---
# <a name="frequently-asked-questions-faq"></a>Часто задаваемые вопросы

### <a name="what-does-my-content-moderator-subscription-include"></a>Что включает в себя подписка Content Moderator?

Ваша подписка Content Moderator включает доступ к API и средства проверки. Вы можете решить, что из них следует использовать, в зависимости от потребностей бизнеса.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Какие ограничения действуют в отношении модерируемого содержимого в API?

Разрешение изображений, используемых этим API, должно быть не меньше 128 пикселей, а размер — не больше 4 МБ. Длина текста должна быть не больше 1024 знаков. Продолжительность видео не ограничена.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Что если передать в API модерации текста или API модерации изображений содержимое, размер которого больше допустимого?

API модерации текста вернет код ошибки, означающий, что текст длиннее, чем допустимо. API модерации изображений также вернет код ошибки, означающий, что изображение не отвечает требованиям к размеру.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Можно сохранить изображения, текст или видео, отправленные на модерацию?

Ваше содержимое принадлежит вам и не может быть сохранено корпорацией Майкрософт без вашего согласия. Корпорация Майкрософт использует передовые в отрасли средства безопасности для защиты содержимого.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Можно ли использовать Content Moderator для проверки изображений на наличие сцен противозаконной эксплуатации детей?

№ Однако уполномоченные организации могут использовать [облачную службу PhotoDNA](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") для этих целей.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Сколько просмотрите команд может соединения пользователя? Можно пользователю переключаться между командами?

Пользователь могла только присоединять одной команды за раз.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Какого рода роли членов команды поддерживаются средства проверки? Чем они отличаются?

В настоящее время средства просмотра позволяет, назначение ролей администратора и редактор. Администраторы могут приглашать других пользователей и имеют доступ к параметрам конфигурации. Рецензенты могут только проверять результаты модерации и добавлять или удалять теги для содержимого.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Что такое тег? Поддерживает ли инструмент проверки настраиваемые теги?

Тег — это одно или двухбуквенный короткий код, который обозначает флаг "умеренность", например «» для взрослых, «r» для материалов непристойного характера и т. д. При необходимости администраторы могут определить новые теги для своей организации.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Сколько участников может быть в моей команде проверки?

Может иметь более пяти участников группы, включая администратора, в группе.
