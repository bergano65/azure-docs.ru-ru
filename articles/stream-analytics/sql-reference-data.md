---
title: Используйте справочные данные базы данных S'L в заданиях Azure Stream Analytics
description: В этой статье описывается, как использовать Базу данных SQL в качестве источника эталонных входных данных для задания Azure Stream Analytics на портале Azure и в Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426505"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Используйте справочные данные из базы данных S'L для задания Azure Stream Analytics

Azure Stream Analytics поддерживает базу данных SQL Azure в качестве источника эталонных входных данных. Базу данных SQL можно использовать как источник эталонных данных для задания Stream Analytics на портале Azure и в Visual Studio с помощью средств Stream Analytics. В этой статье показано, как реализовать оба этих варианта.

## <a name="azure-portal"></a>Портал Azure

Чтобы добавить базу данных SQL Azure в качестве источника эталонных входных данных с помощью портала Azure, выполните приведенные ниже действия.

### <a name="portal-prerequisites"></a>Предварительные требования при использовании портала

1. Создайте задание Stream Analytics.

2. Создайте учетную запись хранения, которая будет использоваться заданием Stream Analytics.

3. Создайте базу данных SQL Azure с набором данных, который будет использоваться как источник эталонных данных в задании Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Определение Базы данных SQL в качестве источника эталонных входных данных

1. В задании Stream Analytics в разделе **Топология задания** выберите **Входные данные**. Щелкните **Добавить ссылочный вход** и выберите **База данных SQL**.

   ![Входные данные задания Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Заполните данные конфигурации для входных данных Stream Analytics. Выберите имя базы данных и сервера, а также имя пользователя и пароль. Если необходимо, чтобы эталонные входные данные периодически обновлялись, щелкните "Вкл.", чтобы задать частоту обновления в формате ДД:ЧЧ:MM. При наличии больших наборов данных, которые часто обновляются, можно использовать [разностный запрос](sql-reference-data.md#delta-query).

   ![Настройка эталонной конфигурации Базы данных SQL](./media/sql-reference-data/sql-input-config.png)

3. Проверьте запрос моментального снимка в редакторе SQL-запросов. Дополнительные сведения см. в статье [Краткое руководство. Использование редактора SQL-запросов на портале Azure для подключения к данным и их запроса](../sql-database/sql-database-connect-query-portal.md).

### <a name="specify-storage-account-in-job-config"></a>Выбор учетной записи хранения в конфигурации задания

В разделе **Настройка** выберите пункт **Параметры учетной записи хранения** и щелкните **Добавить учетную запись хранения**.

   ![Параметры учетной записи хранения Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Запустите задание

После того как вы настроите другие входные и выходные данные, а также запрос, можно запустить задание Stream Analytics.

## <a name="tools-for-visual-studio"></a>Инструменты для Visual Studio

Чтобы добавить базу данных SQL Azure в качестве источника эталонных входных данных с помощью Visual Studio, выполните приведенные ниже действия.

### <a name="visual-studio-prerequisites"></a>Предварительные требования при использовании Visual Studio

1. [Установите инструменты Stream Analytics для Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Поддерживаются следующие версии Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Ознакомьтесь со статьей [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md).

3. Создайте учетную запись хранения.

### <a name="create-a-sql-database-table"></a>Создание таблицы базы данных SQL

Создайте таблицу для хранения эталонных данных, используя SQL Server Management Studio. Дополнительные сведения см. в статье [Руководство. Разработка первой базы данных SQL Azure с использованием SSMS](../sql-database/sql-database-design-first-database.md).

Пример таблицы, используемый в следующем примере, был создан с помощью следующей инструкции:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Выберите свою подписку

1. В Visual Studio в меню **Вид** выберите **Обозреватель серверов**.

2. Щелкните пункт **Azure** правой кнопкой мыши, выберите **Connect to Microsoft Azure Subscription** (Подключиться к подписке Microsoft Azure) и войдите в систему с помощью учетной записи Azure.

### <a name="create-a-stream-analytics-project"></a>Создание проекта Stream Analytics

1. Выберите **Файл > Новый проект**. 

2. Из списка шаблонов слева выберите **Stream Analytics** и щелкните **Azure Stream Analytics Application** (Приложение Azure Stream Analytics). 

3. Введите значения в поля **Имя**, **Расположение** и **Имя решения** для проекта, а затем нажмите кнопку **ОК**.

   ![Новый проект Stream Analytics в Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Определение Базы данных SQL в качестве источника эталонных входных данных

1. Создайте новые входные данные.

   ![Новые входные данные Stream Analytics в Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Дважды щелкните файл **Input.json** в **обозревателе решений**.

3. Заполните **конфигурацию входных данных Stream Analytics**. Выберите имя базы данных, имя сервера, тип и частоту обновления. Укажите частоту обновления в формате `DD:HH:MM`.

   ![Настройка входных данных Stream Analytics в Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Если вы выберете параметр Execute only once (Выполнить только один раз) или Execute periodically (Выполнять периодически), в узле файла **Input.json** проекта будет создан файл SQL с программным кодом, который называется **[Псевдоним входных данных].snapshot.sql**.

   ![Программный код входных данных в Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Если вы выберете параметр Refresh Periodically with Delta (Периодическое разностное обновление), будет создано два файла SQL с программным кодом: **[Псевдоним входных данных].snapshot.sql** и **[Псевдоним входных данных].delta.sql**.

   ![Программный код в обозревателе решений](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Откройте файл SQL в редакторе и напишите SQL-запрос.

5. Если вы используете Visual Studio 2019, и вы установили инструменты s'L Server Data, вы можете протестировать запрос, нажав **на Execute.** Появится окно мастера, которое поможет вам подключиться к базе данных SQL, а результат запроса появится в окне внизу.

### <a name="specify-storage-account"></a>Определение учетной записи хранения

Откройте файл **JobConfig.json**, чтобы указать учетную запись хранения для хранения моментальных снимков эталонных данных SQL.

   ![Настройка задания Stream Analytics в Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Локальное тестирование и развертывание в Azure

Прежде чем развернуть задание в Azure, вы можете локально протестировать логику запроса с использованием реальных входных данных. Дополнительные сведения об этой функции см. в статье [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md). После завершения тестирования щелкните **Отправить в Azure**. Дополнительные сведения о создании задания см. в статье [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="delta-query"></a>Разностный запрос

Вместе с разностным запросом рекомендуется использовать [темпоральные таблицы в базе данных SQL Azure](../sql-database/sql-database-temporal-tables.md).

1. Создайте временную таблицу в базе данных Azure S'L.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Создайте запрос моментального снимка. 

   Используйте параметр ** \@snapshotTime,** чтобы поручить время выполнения Stream Analytics для получения набора справочных данных из временной таблицы базы данных S'L, действительный во время системы. Если этот параметр не указан, существует риск получения неточного базового набора эталонных данных из-за рассинхронизации часов. Ниже показан пример полного запроса моментального снимка.
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Создайте разностный запрос. 
   
   Этот запрос удаляет все строки в вашей базе данных S'L, которые были вставлены или удалены в течение времени начала, ** \@deltaStartTime**, и время ** \@окончания deltaEndTime**. Разностный запрос должен возвращать те же столбцы, что и запрос моментального снимка, а также столбец **_operation_**. Этот столбец определяет, вставляется ли строка или удаляется между ** \@deltaStartTime** и ** \@deltaEndTime.** Итоговые строки помечены как **1**, если записи были вставлены, или **2**, если они были удалены. 

   Темпоральная таблица ведет учет обновленных записей путем записи операций вставки и удаления. Затем среда выполнения Stream Analytics применит результаты разностного запроса к предыдущему моментальному снимку, чтобы обновить эталонные данные. Ниже показан пример разностного запроса.

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Обратите внимание, что в дополнение к разностному запросу среда выполнения Stream Analytics может периодически выполнять запрос моментального снимка для хранения контрольных точек.

## <a name="test-your-query"></a>Тестирование запроса
   Важно убедиться, что ваш запрос возвращает ожидаемый набор данных, который задания Stream Analytics будет использоваться в качестве справочных данных. Чтобы протестировать запрос, перейдите в раздел Ввода в разделе Topology вакансий на портале. Затем можно выбрать примерданные данных на вводе справочной базы данных S'L. После того, как образец станет доступен, вы можете загрузить файл и проверить, если данные возвращаются, как ожидалось. Если вы хотите оптимизировать разработку и протестировать итерации, рекомендуется использовать [инструменты Stream Analytics для Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) Вы также можете использовать любой другой предпочтительный инструмент, чтобы сначала убедиться, что запрос возвращает нужные результаты из базы данных Azure S'L, а затем использовать их в работе Stream Analytics. 

## <a name="faqs"></a>Часто задаваемые вопросы

**Будет ли взиматься дополнительная плата за использование входных эталонных данных SQL в Azure Stream Analytics?**

В задании Stream Analytics нет дополнительных [затрат за единицу потоковой передачи](https://azure.microsoft.com/pricing/details/stream-analytics/). Однако задание Stream Analytics должно иметь связанную учетную запись хранения Azure. Задание Stream Analytics запрашивает базу данных SQL (во время запуска задания и интервала обновления), чтобы получить набор эталонных данных, и сохраняет моментальный снимок в учетной записи хранения. За хранение этих моментальных снимков будет взиматься дополнительная плата, указанная на [странице цен](https://azure.microsoft.com/pricing/details/storage/) на учетную запись хранения Azure.

**Как узнать, что моментальный снимок эталонных данных запрашивается из базы данных SQL и используется в задании Azure Stream Analytics?**

Существует два метрики, отфильтрованные логическим названием (под порталом Metrics Azure), которые можно использовать для мониторинга работоспособности ввода справочных данных базы данных s'L.

   * InputEvents: Эта метрика измеряет количество записей, загруженных из набора справочных данных базы данных s'L.
   * InputEventBytes: Эта метрика измеряет размер снимка справочных данных, загруженного в память о заданиях Stream Analytics. 

С помощью сочетания обеих этих метрик можно определить, запрашивает ли задание базу данных SQL для извлечения набора эталонных данных с последующей загрузкой в память.

**Нужен ли специальный тип базы данных SQL Azure?**

Azure Stream Analytics будет работать с любым типом базы данных SQL Azure. Однако важно понимать, что частота обновления, заданная для эталонных входных данных, может повлиять на загрузку запросов. Чтобы использовать разносные запросы, рекомендуется использовать темпоральные таблицы в базе данных SQL Azure.

**Почему Azure Stream Analytics хранит моментальные снимки в учетной записи хранения Azure?**

Она гарантирует обработку событий только один раз и по крайней мере одну доставку событий. В случаях, когда временные проблемы влияют на задание, для восстановления состояния необходимо выполнить небольшое количество воспроизведений. Чтобы включить воспроизведение, эти моментальные снимки должны храниться в учетной записи хранения Azure. Дополнительные сведения о воспроизведении контрольных точек см. в статье [Концепции контрольных точек и воспроизведения в Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Использование эталонных данных для уточняющих запросов в Stream Analytics](stream-analytics-use-reference-data.md)
* [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
* [Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)](stream-analytics-live-data-local-testing.md)
