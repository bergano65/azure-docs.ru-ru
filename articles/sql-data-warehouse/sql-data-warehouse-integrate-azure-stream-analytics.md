---
title: Использование Azure Stream Analytics
description: Советы по использованию службы Azure Stream Analytics и хранилища данных SQL для разработки решений.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721206"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Использование Azure Stream Analytics с Azure синапсе Analytics
Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения можно узнать, прочитав статью [Общие сведения о Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Вы можете узнать, как создать комплексное решение с Stream Analytics, следуя инструкциям в руководстве по [началу работы с Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

В этой статье вы узнаете, как использовать базу данных хранилища данных в качестве приемника вывода для заданий Stream Analytics.

## <a name="prerequisites"></a>предварительные требования
Сначала необходимо выполнить следующие шаги, описанные в учебнике [Приступая к работе с Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

1. Создание ввода концентратора событий
2. Настройка и запуск приложения генератора событий
3. Подготовка задания Stream Analytics
4. Указание входных данных задания и запроса

Затем создание базы данных хранилища данных SQL Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Указание выходных данных задания: база данных хранилища данных SQL Azure
### <a name="step-1"></a>Шаг 1
В Stream Analytics задании щелкните **выходные данные** в верхней части страницы, а затем нажмите кнопку **Добавить**.

### <a name="step-2"></a>Шаг 2
Выберите База данных SQL.

### <a name="step-3"></a>Шаг 3.
Введите следующие значения на следующей странице:

* *Псевдоним выходных данных*: введите понятное имя для выходных данных этого задания.
* *Подписка*:
  * Если ваша база данных хранилища данных SQL принадлежит к той же подписке, что и задание Stream Analytics, выберите параметр "Использовать базу данных SQL из текущей подписки".
  * Если ваша база данных находится в другой подписке, выберите параметр "Использовать базу данных SQL из другой подписки".
* *База данных*: укажите имя целевой базы данных.
* *Имя сервера*: укажите имя сервера для указанной базы данных. Эти сведения можно узнать на портале Azure.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Шаг 4.
Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

После того как подключение к базе данных будет завершено, на портале появится уведомление. Чтобы проверить подключение к базе данных, можно нажать кнопку тест.

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения об интеграции см. в разделе [Интеграция других служб](sql-data-warehouse-overview-integrate.md).
Дополнительные советы по разработке см. в разделе [решения по проектированию и приемы программирования для хранилищ данных](sql-data-warehouse-overview-develop.md).

