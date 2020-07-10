---
title: Устранение проблем со входом для подписки Azure
description: Эта статья поможет вам устранить проблемы со входом на портал Azure или в Центр управления учетной записью Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: e424a3d0e46f0dc154809eba501d2339bc94b1f5
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806709"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Устранение проблем со входом для подписки Azure

Это руководство поможет вам устранить проблемы со входом на портал Azure или в Центр управления учетной записью Azure.

> [!NOTE]
> Если у вас возникли проблемы при регистрации для получения новой учетной записи Azure, см. статью [Устранение неполадок при регистрации для получения новой учетной записи на портале Azure или в Центре управления учетной записью Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up).

## <a name="page-hangs-in-the-loading-status"></a>Страница зависает в состоянии загрузки

Если страница браузера зависает, поочередно выполните все описанные действия, пока не войдете на портал Azure.

- Обновите страницу.
- Воспользуйтесь другим браузером.
- Используйте в браузере режим закрытого просмотра.

   - **Edge.** Откройте раздел **Параметры** (три точки рядом с изображением профиля), выберите **Новое окно InPrivate**, а затем перейдите на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.azure.com/Subscriptions). 
   - **Chrome.** Выберите режим **Incognito** (Инкогнито).
   - **Safari.** Выберите **File** (Файл) и **New Private Window** (Новое окно в конфиденциальном режиме).

- Очистите кэш и удалите файлы cookie.

   - **Edge.** Откройте раздел **Параметры** и выберите **Конфиденциальность и службы**. Выполните действия, описанные в разделе **Очистить данные браузера**. Убедитесь, что выбраны флажки **Журнал браузера**, **Журнал скачиваний** и **Кэшированные изображения и файлы**, а затем щелкните **Удалить**.
   - **Chrome.** Выберите **Settings** (Параметры) и щелкните **Clear browsing data** (Очистить данные браузера) в разделе **Privacy and Security** (Конфиденциальность и безопасность).

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Выполняется автоматический вход от имени другого пользователя

Эта проблема может возникать, если вы используете при работе с браузером несколько учетных записей пользователя.

Для устранения этой проблемы воспользуйтесь одним из указанных ниже способов.

- Очистите кэш и удалите файлы cookie Интернета.

   - **Edge.** Откройте раздел **Параметры** и выберите **Конфиденциальность и службы**. Выполните действия, описанные в разделе **Очистить данные браузера**. Убедитесь, что выбраны флажки **Журнал просмотра**, **Журнал загрузки**, **Файлы cookie** и **Кэшированные изображения и файлы**, а затем щелкните **Удалить**.
   - **Chrome.** Выберите **Settings** (Параметры) и щелкните **Clear browsing data** (Очистить данные браузера) в разделе **Privacy and Security** (Конфиденциальность и безопасность).
- Сбросьте параметры браузера до значений по умолчанию.
- Используйте в браузере режим закрытого просмотра. 
   - **Edge.** Откройте раздел **Параметры** (три точки рядом с изображением профиля), выберите **Новое окно InPrivate**, а затем перейдите на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.azure.com/Subscriptions). 
   - **Chrome.** Выберите режим **Incognito** (Инкогнито).
   - **Safari.** Выберите **File** (Файл) и **New Private Window** (Новое окно в конфиденциальном режиме).

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Я могу войти, но вижу сообщение об ошибке с информацией о том, что подписки не найдены

Эта проблема возникает, если вы выбрали неправильный каталог или у учетной записи нет необходимых разрешений.

**Сценарий 1.** Вы получаете сообщение об ошибке входа на [портал Azure](https://portal.azure.com/)

Чтобы устранить эту проблему:

- Убедитесь, что выбран правильный каталог Azure, щелкнув учетную запись в правом верхнем углу.
- Если выбран правильный каталог Azure, но вы по-прежнему получаете сообщение об ошибке, [добавьте учетную запись с правами владельца](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**Сценарий 2.** Вы получаете сообщение об ошибке входа в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1.  Войдите в [представление подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Выберите подписку, которую требуется проверить, а затем щелкните **Параметры**.
1.  Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .

## <a name="additional-help-resources"></a>Дополнительные справочные ресурсы

Другие статьи по устранению проблем при выставлении счетов и с подписками Azure

- [Карта не принимается](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Проблемы с регистрацией подписки](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [Подписки не найдены](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Просмотр стоимости Enterprise отключен](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Документация по выставлению счетов в Azure](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Свяжитесь с нами для получения помощи

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
