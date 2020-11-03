---
title: Службы коммуникации Azure — пример веб-вызова
titleSuffix: An Azure Communication Services sample
description: Сведения о примере веб-вызова Служб коммуникации Azure
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6fc3441fac5fe037e9a268d26012761d1fece70
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463227"
---
# <a name="get-started-with-the-web-calling-sample"></a>Начало работы с примером веб-вызова

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Этот пример можно найти на сайте GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

**Пример веб-вызова** Служб коммуникации Azure демонстрирует, как использовать клиентскую библиотеку вызовов Служб коммуникации для создания вызова с помощью JavaScript.

Из краткого руководства мы узнаем, как работает пример и запустим его на локальном компьютере. Затем мы развернем пример в Azure с помощью собственных ресурсов Служб коммуникации Azure.

## <a name="overview"></a>Обзор

Примером веб-вызова является веб-приложение, служащее в качестве пошагового руководства по различным возможностям, которые предоставляет клиентская библиотека веб-вызовов Служб коммуникации. 

Этот пример был создан для разработчиков и позволяет легко приступить к работе со Службами коммуникации. Его пользовательский интерфейс делится на несколько разделов, каждый из которых содержит кнопку отображения кода, с помощью которой можно копировать код непосредственно из браузера в собственное приложение Служб коммуникации.

Если на компьютере выполняется [пример веб-вызова](https://github.com/Azure-Samples/communication-services-web-calling-tutorial), вы увидите следующую целевую страницу:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Учебник по веб-вызовам 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Учебник по веб-вызовам 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>Подготовка пользователей и инициализация пакета SDK 

Чтобы приступить к работе с роликом, введите строку подключения из [ресурса Служб коммуникации](../quickstarts/create-communication-resource.md) в `config.json`. Она будет использоваться для подготовки [маркера доступа пользователя](../concepts/authentication.md), чтобы можно было инициализировать пакет SDK вызовов.

Введите свой личный идентификатор в поле входных данных для удостоверения пользователя. Если здесь ничего не указано, то будет создано случайное удостоверение пользователя. 

Щелкните Provisioning user and initialize SDK (Подготовка пользователя и инициализация пакета SDK), чтобы инициализировать пакет SDK с помощью маркера, подготовленного службой подготовки маркеров серверной части. Эта серверная служба находится в `/project/webpack.config.js`.

Нажмите кнопку "Показать код", чтобы просмотреть пример кода, который можно использовать в своем решении.

После инициализации пакета SDK на экране должно отображаться следующее:

:::image type="content" source="./media/user-provisioning.png" alt-text="Подготовка пользователей" lightbox="./media/user-provisioning.png":::

Теперь можно приступить к размещению вызовов с помощью ресурса Служб коммуникации.

## <a name="placing-and-receiving-calls"></a>Размещение и получение вызовов

Пакет SDK веб-вызовов для Служб коммуникации позволяет использовать режимы **1:1** , **1:N** и **групповые** вызовы.

Для исходящих вызовов 1:1 или 1:N можно указать несколько удостоверений пользователей Служб коммуникации, чтобы осуществить вызов с помощью значений, разделенных запятыми. Вы также можете указать традиционные номера телефонов (ТСОП) для вызова с помощью значений, разделенных запятыми. 

При вызове телефонных номеров ТСОП укажите альтернативный идентификатор звонящего. Нажмите кнопку "Позвонить", чтобы совершить исходящий звонок:

:::image type="content" source="./media/place-a-call.png" alt-text="Осуществление вызовов" lightbox="./media/place-a-call.png":::

Чтобы присоединиться к групповому вызову, введите идентификатор GUID, который определяет вызов, и нажмите кнопку "Присоединиться к группе":

:::image type="content" source="./media/join-a-group-call.png" alt-text="Присоединение к групповому вызову" lightbox="./media/join-a-group-call.png":::

Нажмите кнопку "Показать код", чтобы просмотреть пример кода для совершения вызовов, получения вызовов и присоединения к групповым вызовам.

Активный вызов выглядит следующим образом:

:::image type="content" source="./media/group-call.png" alt-text="Групповой вызов" lightbox="./media/group-call.png":::

В примере также есть фрагменты кода для следующих возможностей:

  - Включение или отключение видеокамеры по щелчку значка видео.
  - Включение или отключение микрофона по щелчку значка микрофона.
  - Удержание вызова или прекращение его удержания по щелчку значка воспроизведения.
  - Запуск или отмена демонстрации своего экрана по щелчку иконки экрана.
  - Добавление участника к вызову по щелчку значка пользователя.
  - Удаление определенного участника из вызова по щелчку "Удалить участника" в списке участников.


## <a name="next-steps"></a>Дальнейшие действия

>[!div class="nextstepaction"] 
>[Скачайте пример с GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Дополнительные сведения см. в следующих статьях:

- Изучите, как правильно [использовать клиентскую библиотеку вызовов](../quickstarts/voice-video-calling/calling-client-samples.md).
- Узнайте больше о [принципе работы функции вызовов](../concepts/voice-video-calling/about-call-types.md).
- Ознакомьтесь со [справочной документацией по API](https://docs.microsoft.com/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js).

## <a name="additional-reading"></a>Дополнительные материалы

- [Службы коммуникации Azure (GitHub)](https://github.com/Azure/communication) — дополнительные примеры и сведения на официальной странице GitHub
- [Redux](https://redux.js.org/) — управление состоянием на стороне клиента
- [FluentUI](https://aka.ms/fluent-ui) — библиотека пользовательского интерфейса, поддерживаемая корпорацией Майкрософт
- [React](https://reactjs.org/) — библиотека для создания пользовательских интерфейсов
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) — платформа для создания веб-приложений
