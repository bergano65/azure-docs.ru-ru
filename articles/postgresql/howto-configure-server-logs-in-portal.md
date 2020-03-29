---
title: Управление журналами - Портал Azure - База данных Azure для PostgreS'L - Единый сервер
description: В этой статье описывается, как настроить и получить доступ к журналам серверов (.log файлы) в базе данных Azure для PostgreS-L - Единый сервер с портала Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763697"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Найдите и получите доступ к базе данных Azure для PostgreS'L - Журналы единого сервера с портала Azure

Вы можете настроить, перечислить и загрузить [базу данных Azure для журналов PostgreS'L](concepts-server-logs.md) с портала Azure.

## <a name="prerequisites"></a>Предварительные требования
Шаги в этой статье требуют, чтобы у вас была [база данных Azure для сервера PostgreS'L.](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Настройка журнала
Настройте доступ к журналам запросов и журналам ошибок. 

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите сервер базы данных Azure для PostgreSQL.

3. В разделе **Мониторинг** в боковой панели выберите **журналы Сервера.** 

   ![Скриншот параметров журналов Сервера](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Чтобы увидеть параметры сервера, выберите **Нажмите здесь, чтобы включить журналы и настроить параметры журнала.**

5. Измените параметры, которые необходимо настроить. Все изменения, вносимые в этом сеансе, выделены фиолетовым цветом.

   После изменения параметров выберите **Сохранить**. Или вы можете отказаться от изменений. 

   ![Скриншот параметров параметров сервера](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Со **страницы Параметры сервера** можно вернуться к списку журналов, закрыв страницу.

## <a name="view-list-and-download-logs"></a>Просмотр списка журналов и их скачивание
После начала регистрации можно просмотреть список доступных журналов и загрузить отдельные файлы журнала. 

1. Откройте портал Azure.

2. Выберите сервер базы данных Azure для PostgreSQL.

3. В разделе **Мониторинг** в боковой панели выберите **журналы Сервера.** Страница показывает список файлов журнала.

   ![Скриншот страницы журналов Server со списком выделится журналами](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Действует следующее соглашение об именовании журналов: **postgresql-гггг-мм-дд_чч0000.log**. Дата и время, используемое в имени файла, — это время, когда был выпущен журнал. Файлы журнала вращаются каждый час или 100 МБ, в зависимости от того, что наступит раком.

4. При необходимости используйте поле поиска, чтобы быстро сузить до определенного журнала, в зависимости от даты и времени. Поиск осуществляется по имени журнала.

   ![Скриншот страницы журналов Server с выделенной коробкой поиска и результатами](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Чтобы загрузить отдельные файлы журнала, выберите значок внизовой стрелки рядом с каждым файлом журнала в строке таблицы.

   ![Скриншот страницы журналов Сервера с выделенной иконой вниз-стрелки](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Дальнейшие действия
- Смотрите [журналы сервера Access в CLI,](howto-configure-server-logs-using-cli.md) чтобы узнать, как загружать журналы программно.
- Узнайте больше о [журналах серверов](concepts-server-logs.md) в базе данных Azure для PostgreS'L. 
- Для получения более подробной информации об определениях параметров и журнале PostgreS'L см. [error reporting and logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)

