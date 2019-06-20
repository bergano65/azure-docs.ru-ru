---
title: Устранение неполадок выполнения пакетов в среде выполнения интеграции SSIS | Документация Майкрософт
description: В этой статье содержатся рекомендации по устранению неполадок для выполнения пакета служб SSIS в среде выполнения интеграции SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 7789970b47f0e55adee5bbe9da9f303aee6cdb25
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190124"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Устранение неполадок выполнения пакетов в среде выполнения интеграции SSIS

Эта статья содержит наиболее распространенные ошибки, которые вы можете достигнуть при выполнении SSIS пакеты в среде выполнения интеграции SSIS, потенциальные причины и действия для устранения ошибки.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Где найти журналы для устранения неполадок

* На портале фабрики данных AZURE можно использовать для проверки выходных данных действия выполнения пакета служб SSIS, включая результат выполнения, сообщения об ошибках и идентификатор операции. Сведения можно найти на [мониторинг конвейера](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* Каталог служб SSIS (SSISDB) можно использовать для проверки сведений журналов для выполнения. Подробности можно найти в [наблюдение за выполнением пакетов и других операций](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Распространенные ошибки, причины и решения

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Сообщение об ошибке: `"Connection Timeout Expired."` или `"The service has encountered an error processing your request. Please try again."`

* Возможной причиной & Рекомендуемое действие:
  * Повторная загрузка данных источника и назначения. Проверьте, какую нагрузку на ваши данные источника и назначения и см. в разделе, имеет ли она Недостаточно емкости. Например если используется Azure SQL, рекомендуется рассмотреть масштабирования вверх, если база данных, скорее всего, время ожидания.
  * Сетевое соединение между среды выполнения интеграции SSIS и данных источника и назначения нестабильна, особенно в том случае, если подключение установлено между регионами и между локальной сетью и azure. Рекомендуется для применения шаблона повторных попыток в пакете служб SSIS в соответствии с инструкциями:
    * Убедитесь, что пакетов служб SSIS можно повторно запустить в случае сбоя без побочных эффектов (например. потери данных, данных dup...)
    * Настройка **повторите** и **интервал повторных попыток** выполнение действия пакета служб SSIS на вкладке "Общие" ![задать свойства на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Для компонента с ADO.NET и OLEDB источника и назначения ConnectRetryCount и ConnectRetryInterval могут быть установлены в диспетчер соединений в пакет служб SSIS или действия SSIS

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Сообщение об ошибке: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Возможной причиной & Рекомендуемое действие:
  * Эта проблема обычно означает, что данные источника и назначения является недоступным из среды выполнения интеграции SSIS, который может быть вызвана по разным причинам:
    * Убедитесь, что правильно передается данных источника и назначения имени и IP-адрес
    * Убедитесь, что брандмауэр настроен должным образом
    * Убедитесь, что виртуальная сеть настроена должным образом, если данные источника и назначения находятся в локальной.
      * Чтобы узнать, является ли неполадка из конфигурации виртуальной сети путем подготовки виртуальной Машины Azure в той же виртуальной сети. И проверьте, доступны ли данные источника и назначения из виртуальной Машины Azure
      * Дополнительные сведения об использовании среды выполнения интеграции SSIS в виртуальной сети можно найти [присоединить среду выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Сообщение об ошибке: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Возможной причиной & Рекомендуемое действие:
  * Поставщик ADO.NET, используемые в пакете не установлен в среде выполнения интеграции SSIS. Поставщик можно установить с помощью Выборочная установка. Дополнительные сведения о Выборочная установка можно найти в [настроить программу установки для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Сообщение об ошибке: "`The connection '...' is not found`"

* Возможной причиной & Рекомендуемое действие:
  * Эта ошибка может быть, так как известная проблема в старой версии SSMS. Если пакет содержит пользовательский компонент (например, пакет дополнительных компонентов служб SSIS Azure или компонентов сторонних производителей), которая не установлена на компьютере, где используется среда SSMS для развертывания, компонент будет прекращена с SSMS и вызывает ошибку. Обновление [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) до последней версии, проблема устранена.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Сообщение об ошибке: «Не достаточно места на диске»

* Возможной причиной & Рекомендуемое действие:
  * Эта ошибка означает, что локальный диск используется в узле среды выполнения интеграции SSIS. Проверьте, ли пакет или Выборочная установка, потребляют много пространства диска.
    * Если диск используется пакет, он будет освободить после завершения выполнения пакета.
    * Если диск используется вашей Выборочная установка, необходимо остановить среды выполнения интеграции SSIS, измените скрипт и снова запустить среды выполнения интеграции SSIS. Весь контейнер больших двоичных объектов Azure, указанной для Выборочная установка будут копироваться в него узел среды выполнения Интеграции SSIS, поэтому проверьте, существует ли все ненужное в этом контейнере.

### <a name="error-message-cannot-open-file-"></a>Сообщение об ошибке: «Не удается открыть файл «...»»

* Возможной причиной & Рекомендуемое действие:
  * Эта ошибка возникает при выполнении пакета не удается найти файл на локальном диске в среде выполнения интеграции SSIS.
    * Не рекомендуется использовать абсолютный путь в пакете, выполнение в среде выполнения интеграции SSIS. Использовать текущий рабочий каталог выполнения (.) или временной папки (% TEMP %) Вместо этого.
    * Если это необходимо для сохранения некоторых файлов на узлах SSIS Integration Runtime, рекомендуется подготовить файлов с помощью [настройки установки](how-to-configure-azure-ssis-ir-custom-setup.md). После завершения выполнения, будут удалены все файлы в рабочем каталоге выполнения.
    * Другой вариант — использовать файл Azure вместо сохранения файла в узле среды выполнения интеграции SSIS. Более подробные сведения можно найти в [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Сообщение об ошибке: ««SSISDB» база данных достигла предельного размера»

* Возможной причиной & Рекомендуемое действие:
  * SSISDB, созданные в Azure SQL или управляемого экземпляра, при создании среды выполнения интеграции SSIS была достигнута квота.
    * Попробуйте увеличить DTU базы данных, чтобы устранить эту проблему. Сведения можно найти на [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Проверьте, будет ли пакет создавать многие журналы. Если это так, чтобы очистить эти журналы можно настроить задания обработки эластичных БД. Ссылаться на [очистки журналов SSISDB с помощью задания эластичной базы данных Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) для детализации.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Сообщение об ошибке: «Количество запросов для базы данных —... и он достигнут.»

* Возможной причиной & Рекомендуемое действие:
  * Если многие пакеты выполняются в параллельном режиме в среде выполнения интеграции SSIS, эта ошибка может возникать, поскольку достигается ограничение запроса в SSISDB. Рассмотрите возможность увеличения DTC в SSISDB, чтобы устранить эту проблему. Сведения можно найти на [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Сообщение об ошибке: «Сбой операции служб SSIS с состояние операции Непредвиденная:...»

* Возможной причиной & Рекомендуемое действие:
  * Ошибка обычно вызвана временная ошибка, поэтому следует повторно запустить выполнение пакета. Рекомендуется для применения шаблона повторных попыток в пакете служб SSIS в соответствии с инструкциями:
    * Убедитесь, что пакетов служб SSIS можно повторно запустить в случае сбоя без побочных эффектов (например, потери данных, данных dup...)
    * Настройка **повторите** и **интервал повторных попыток** выполнение действия пакета служб SSIS на вкладке "Общие" ![задать свойства на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Для компонента с ADO.NET и OLEDB источника и назначения ConnectRetryCount и ConnectRetryInterval могут быть установлены в диспетчер соединений в пакет служб SSIS или действия SSIS

### <a name="error-message-there-is-no-active-worker"></a>Сообщение об ошибке: «Есть нет активных рабочих».

* Возможной причиной & Рекомендуемое действие:
  * Эта ошибка обычно означает, что среда выполнения интеграции SSIS находится в неработоспособном состоянии. Проверьте портал Azure для статус и подробности ошибки: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Сообщение об ошибке: «Среды выполнения интеграции не могут быть обновлены и в конечном итоге перестанет работать, так как мы не доступ к контейнеру BLOB-объектов Azure, заданное для пользовательской установки.»

* Эта ошибка возникает, когда среда выполнения интеграции SSIS не может получить доступ к хранилищем, настроенным для Выборочная установка. Проверка ли указанный универсальный код ресурса SAS является допустимым и еще не истек.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Сообщение об ошибке: «Поставщик Microsoft OLE DB для служб Analysis Services. "Hresult: 0x80004005 Описание: " Ошибка COM: Ошибка COM: mscorlib; Исключения по целевому объекту вызова»

* Возможной причиной & Рекомендуемое действие:
  * Одной из возможных причин является, имя пользователя и пароль с поддержкой MFA настроено для проверки подлинности служб Azure Analysis Services, который еще не поддерживается в среде выполнения интеграции SSIS. Попробуйте использовать субъект-службу для проверки подлинности служб Azure Analysis Service:
    1. Подготовьте субъект-служба консультантам [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. В диспетчере соединений, Настройка «Использовать указанные имя пользователя и пароль»: «AppID» в качестве имени пользователя и «clientSecret» в качестве пароля

### <a name="package-takes-unexpected-long-time-to-execute"></a>Пакет принимает Непредвиденная продолжительное время

* Возможной причиной & Рекомендуемое действие:
  * Слишком много операций выполнения пакетов запланирована на среду выполнения интеграции SSIS. В этом случае все выполнения будет ожидать в очереди для очереди для выполнения.
    * Максимальное количество параллельных выполнений на IR = число узлов * максимальное количество параллельных выполнения на каждом узле
    * Ссылаться на [создать Azure-SSIS Integration Runtime в фабрике данных Azure](create-azure-ssis-integration-runtime.md) по настройке, количество узлов и Max параллельного выполнения на каждом узле.
  * Среда выполнения интеграции SSIS остановлена или в неработоспособном состоянии. Проверьте [среды выполнения интеграции Azure SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime) по проверке состояния среды выполнения интеграции SSIS и ошибки.
  * Рекомендуется задать время ожидания, если вы уверены, что выполнение пакета должна быть выполнена в определенное время: ![Задать свойства на вкладке "Общие"](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Низкая производительность при выполнении пакета

* Возможной причиной & Рекомендуемое действие:

  * Проверьте, находится ли среда выполнения интеграции SSIS в том же регионе, в качестве источника данных и назначения.

  * Включить уровень ведения журнала «Производительность»

      Можно задать уровень ведения журнала выполнения пакета для «Производительность», чтобы собирать дополнительные сведения о продолжительности детализации для каждого компонента в среде выполнения. Сведения можно найти на: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Проверьте производительность узла среды выполнения Интеграции в страницу "Отслеживание" среды выполнения Интеграции на портале Azure.
    * Способы отслеживания среды выполнения интеграции SSIS: [Среда выполнения интеграции Azure SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Журнал использования ЦП и памяти среды выполнения интеграции SSIS доступна на метрики фабрики данных на портале Azure ![мониторинг метрик среды выполнения интеграции SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
