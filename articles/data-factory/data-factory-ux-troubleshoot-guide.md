---
title: Устранение неполадок с интерфейсом фабрики данных Azure
description: Узнайте, как устранять неполадки с помощью UX фабрики данных Azure.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87568010"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Устранение неполадок с UX в фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются распространенные методы устранения неполадок в службе "Фабрика данных Azure".

## <a name="adf-ux-not-loading"></a>UI ADF не загружается

> [!NOTE]
> Служба фабрики данных Azure официально поддерживает Microsoft ребра и Google Chrome. Использование других веб-браузеров может привести к непредвиденному или недокументированному поведению.

### <a name="third-party-cookies-blocked"></a>Сторонние файлы cookie заблокированы

Пользовательский интерфейс ADF использует файлы cookie браузера для сохранения пользовательского сеанса и обеспечения интерактивной разработки и мониторинга. Возможно, ваш браузер блокирует сторонние файлы cookie, так как вы используете сеанс режиме инкогнито или включили блокирование AD. Блокирование файлов cookie сторонних производителей может вызвать проблемы при загрузке портала, например перенаправлять на пустую страницу, https://adf.azure.com/accesstoken.html или получить предупреждающее сообщение о том, что сторонние файлы cookie заблокированы. Чтобы решить эту проблему, включите сторонние параметры файлов cookie в браузере, выполнив следующие действия.

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Разрешить все файлы cookie

1. Откройте **Chrome://Settings/cookies** в браузере.
1. Выберите параметр **Разрешить все файлы cookie** ![ Chrome-разрешить-все-файлы cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Обновите ADF UI и повторите попытку.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Разрешить использовать файлы cookie только в UI ADF
Если вы не хотите разрешать все файлы cookie, при необходимости можно просто разрешить использование ADF:
1. Посетите **Chrome://Settings/cookies**.
1. Нажмите кнопку **Добавить** в разделе **сайты, которые всегда могут использовать файлы cookie** . Добавление пользовательского интерфейса ![ ADF на разрешенные сайты](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Добавить сайт **ADF.Azure.com** , проверить **все файлы cookie** и сохранить. ![Разрешить все файлы cookie с сайта UX ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Обновите ADF UI и повторите попытку.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Откройте **Edge://Settings/Content/cookies** в браузере.
1. Убедитесь, что флажок **Разрешить сайтам сохранять и читать данные cookie** включен, а параметр **блокировать сторонние файлы cookie** отключен, ![ разрешающий все файлы cookie в пограничных](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Обновите ADF UI и повторите попытку.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Разрешить использовать файлы cookie только в UI ADF

Если вы не хотите разрешать все файлы cookie, при необходимости можно просто разрешить использование ADF:

1. Посетите **Edge://Settings/Content/cookies**.
1. В разделе " **Разрешить** " щелкните **Добавить** и добавить сайт **ADF.Azure.com** . ![Добавление интерфейса ADF в разрешенные сайты](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Обновите ADF UI и повторите попытку.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

* [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
* [Форум Stack Overflow по Фабрике данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
* [Видео по Azure](https://azure.microsoft.com/resources/videos/index/)
* [Раздел вопросов и ответов на сайте Майкрософт](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
