---
title: Руководство по началу работы с центром знаний Synapse
description: Из этого учебника вы узнаете, как использовать центр знаний Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 47b8c45e22569cc758d00fb8534f409ecebf58ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299937"
---
# <a name="explore-the-synapse-knowledge-center"></a>Изучение центра знаний Synapse

Из этого учебника вы узнаете, как использовать центр знаний Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Начало работы с центром знаний

Существует два способа поиска центра знаний в Synapse Studio:

  1. В корневом центре в разделе "Полезные ссылки" щелкните первую ссылку **Центр знаний**.
  2. В строке меню в верхней части щелкните **?** , а затем — **Центр знаний**.

Выберите любой метод и откройте **центр знаний**.

## <a name="overview"></a>Обзор

**Центр знаний** позволяет выполнить три действия:
* **Use samples immediately** (Использовать примеры немедленно). Этот параметр оптимизирован для анализа в действии как можно быстрее. Если вам нужен краткий пример того, как работает Synapse, выберите этот параметр.
* **Browser available sample** (Просмотреть доступный пример). Этот параметр позволяет связать примеры наборов данных и добавить пример кода в виде скриптов SQL, записных книжек и конвейеров.
* **Tour Synapse studio** (Обзор Synapse Studio). С помощью этого параметра можно перейти к краткому обзору основных частей Synapse Studio. Этот параметр будет для вас полезным, если ранее вы никогда не использовали Synapse Studio.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>Изучение хранилища BLOB-объектов с помощью SQL по запросу

1. В разделе **Центр знаний** щелкните **Use samples immediately** (Использовать примеры немедленно).
1. Выберите **Query data with SQL** (Запросить данные с помощью SQL). 
1. Щелкните **Use samples immediately** (Использовать примеры немедленно).
1. Будет создан новый скрипт SQL.
1. Прокрутите до первого запроса (строки с 28 по 32) и выберите текст запроса.
1. Щелкните "Выполнить". Выбранный текст будет выполнен.

## <a name="loading-more-nyc-taxi-data"></a>Загрузка дополнительных данных NYC Taxi
1. В разделе **Центр знаний** щелкните **Browse available samples** (Просмотреть доступные примеры). 
1. Выберите вкладку **Скрипты SQL** в верхней части экрана
1. Выберите **Load the New York Taxicab dataset** (Загрузить набор данных New York Taxicab).
1. В разделе **Входные данные** выберите **Выбрать существующий пул**, а затем — **SQLDB1**.
1. Щелкните **Открыть скрипт**
1. Появится новый скрипт SQL.
1. Нажмите кнопку **Выполнить**
1. Будет создано несколько таблиц для всех данных NYC Taxi, а после они будут загружены с помощью команды T-SQL COPY.

## <a name="next-steps"></a>Дальнейшие действия

* [Начало работы с Azure Synapse Analytics](get-started.md)
* [Создание рабочей области](quickstart-create-workspace.md)
* [Использование службы SQL по запросу](quickstart-sql-on-demand.md)
