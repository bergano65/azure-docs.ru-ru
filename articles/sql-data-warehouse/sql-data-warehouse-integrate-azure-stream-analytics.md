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
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685718"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Работа со службой Azure Stream Analytics и хранилищем данных SQL
Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения можно узнать, прочитав статью [Общие сведения о Azure Stream Analytics][Introduction to Azure Stream Analytics]. Вы можете узнать, как создать комплексное решение с Stream Analytics, следуя инструкциям в руководстве по [началу работы с Azure Stream Analytics][Get started using Azure Stream Analytics] .

В этой статье вы научитесь выводить данные для заданий Stream Analytics с помощью базы данных Хранилища данных SQL Azure.

## <a name="prerequisites"></a>Предварительные требования
Сначала необходимо выполнить следующие шаги, описанные в учебнике [Приступая к работе с Azure Stream Analytics][Get started using Azure Stream Analytics] .  

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

![][server-name]

* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.

![][add-database]

### <a name="step-4"></a>Шаг 4.
Нажмите кнопку "Проверка", чтобы добавить выходные данные этого задания и убедиться, что Stream Analytics может подключиться к базе данных.

После того как подключение к базе данных будет завершено, на портале появится уведомление. Чтобы проверить подключение к базе данных, можно нажать кнопку тест.

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения об интеграции см. в разделе [Общие сведения об интеграции хранилища данных SQL][SQL Data Warehouse integration overview]Azure.

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
