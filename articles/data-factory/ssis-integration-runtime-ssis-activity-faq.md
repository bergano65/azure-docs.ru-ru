---
title: Выполнение пакета Troubleshoot во время выполнения интеграции SSIS
description: В этой статье содержатся рекомендации по устранению неполадок для выполнения пакета SSIS во время выполнения интеграции SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414685"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Выполнение пакета Troubleshoot во время выполнения интеграции SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Эта статья содержит наиболее распространенные ошибки, которые можно найти при запуске пакетов интеграции серверов S'L Server (SSIS) в момент выполнения интеграции SSIS. В нем описаны потенциальные причины и действия по устранению ошибок.

## <a name="where-to-find-logs-for-troubleshooting"></a>Где найти журналы для устранения неполадок

Используйте портал Azure Data Factory для проверки вывода деятельности по выполнению пакета SSIS. Выход включает результат выполнения, сообщения об ошибках и идентификатор операции. Для получения подробной информации [см.](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

Используйте каталог SSIS (SSISDB) для проверки журналов деталей для выполнения. Для получения подробной информации [см.](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solutions"></a>Распространенные ошибки, причины и решения

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Сообщение об ошибке: "Время подключения истек" или "Служба столкнулась с ошибкой обработки запроса. Повторите попытку".

Вот потенциальные причины и рекомендуемые действия:
* Источник данных или пункт назначения перегружен. Проверьте нагрузку на источник данных или пункт назначения и узнайте, достаточно ли у него мощности. Например, если вы использовали базу данных Azure S'L, подумайте о масштабировании, если база данных может быть временно йетворина.
* Сеть между временем выполнения интеграции SSIS и источником данных или пунктом назначения нестабильна, особенно когда соединение является межрегионевым или между наместах и Azure. Примените шаблон повтора в пакете SSIS, выдыхая следующие действия:
  * Убедитесь, что пакеты SSIS могут быть перезапущены при сбое без побочных эффектов (например, потеря данных или дублирование данных).
  * Наконфигурируете интервал **Retry** и **Retry** активности пакета ![Выполнения **SSIS** на **общей** вкладке.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Для ADO.NET и компонента источника или назначения OLE DB установите **ConnectRetryCount** и **ConnectRetryInterval** в наборе подключения в пакете SSIS или sSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Сообщение об ошибке: "ADO NET Источник не удалось приобрести соединение '...'" с "Сетевой или экземпляр-специфической ошибки произошло при установлении подключения к серверу S'L. Сервер не найден или недоступен".

Причина этой проблемы обычно заключается в том, что источник данных или назначение недоступны из среды выполнения интеграции MSSQL Integration Services. Причины могут быть разными. Попробуйте выполнить следующие действия:
* Убедитесь, что вы передаете источник данных или имя назначения / IP правильно.
* Убедитесь, что брандмауэр установлен должным образом.
* Убедитесь, что ваша виртуальная сеть настроена должным образом, если ваш источник данных или пункт назначения находится на месте:
  * Вы можете проверить, связана ли проблема с виртуальной конфигурацией сети, подготовив VM Azure в той же виртуальной сети. Затем проверьте, можно ли получить доступ к источнику данных или пункту назначения из Azure VM.
  * Более подробную информацию об использовании виртуальной сети с интеграцией SSIS можно найти в момент [выполнения интеграции Azure-SSIS в виртуальную сеть.](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Сообщение об ошибке: "ADO NET Источник не удалось приобрести соединение '...'" с "Не удалось создать управляемый менеджер соединения".

Потенциальная причина заключается в том, что поставщик ADO.NET, используемый в пакете, не установлен в времени выполнения интеграции SSIS. Вы можете установить поставщика с помощью пользовательской настройки. Более подробную информацию можно найти в настройках [настройки для выполнения интеграции Azure-SSIS.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Сообщение об ошибке: "Соединение '...' не найдено"

Эту ошибку может вызывать известная проблема, существующая в предыдущих версиях SQL Server Management Studio (SSMS). Если пакет содержит пользовательский компонент (например, пакет дополнительных компонентов Azure для MSSQL Integration Services или компоненты партнера), который не установлен на компьютере, на котором используется среда SSMS для развертывания, эта среда удалит компонент и выдаст сообщение об ошибке. Обновление [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) до последней версии, которая имеет проблему исправлена.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Сообщение об ошибке:"Код выхода исполнителя SSIS: -1073741819."

* Возможная причина и рекомендуемое действие.
  * Эта ошибка может быть связана с ограничением для источника и назначения Excel, когда несколько источников или направлений Excel исполняются параллельно в многотопомнеи. Обойти это ограничение можно, изменив компоненты Excel для выполнения в последовательности, или разделить их на разные пакеты и вызвать через "Выполнить задачу пакета" с свойством ExecuteOutProcess, установленным как True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Сообщение об ошибке: "На диске не хватает места"

Эта ошибка означает, что локальный диск используется в узло времени выполнения интеграции SSIS. Проверьте, потребляет ли ваш пакет или пользовательская настройка много дискового пространства:
* Если диск потребляется пакетом, он будет освобожден после завершения выполнения пакета.
* Если диск потребляется пользовательской настройкой, необходимо остановить время выполнения интеграции SSIS, изменить сценарий и снова начать время выполнения интеграции. Весь контейнер Azure blob, указанный для пользовательской настройки, будет скопирован в узел времени выполнения интеграции SSIS, поэтому проверьте, есть ли под этим контейнером ненужное содержимое.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Сообщение об ошибке: "Не удалось получить ресурс от мастера. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Описание: Загрузка файла «Я» не удалось.»

* Возможная причина и рекомендуемое действие.
  * Если SSIS Activity является выполнением пакета из файловой системы (файл пакета или файл проекта), эта ошибка будет возникать, если файл проекта, пакета или конфигурации недоступен с учетными данными доступа к пакету, которые вы предоставили в SSIS Activity
    * Если вы используете файл Azure:
      * Путь файла должен \\ \\ \<начинаться\>с\\\<имени учетной записи хранилища .file.core.windows.net пути общего обмена файла\>
      * Домен должен быть "Azure"
      * Имя пользователя должно \<быть именем учетной записи хранилища\>
      * Пароль должен \<быть ключом доступа к хранилищу\>
    * Если вы используете файл на территории, пожалуйста, проверьте, правильно ли настроены учетные данные и разрешение доступа к пакету, чтобы время выполнения интеграции Azure-SSIS можно было получить доступ к вашей совместной доле файла

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Сообщение об ошибке: "Имя файла '...' указанный в соединении не действителен"

* Возможная причина и рекомендуемое действие.
  * Указано недействительное имя файла
  * Убедитесь, что вы используете F'DN (Полностью квалифицированное доменное имя) вместо короткого времени в вашем менеджере соединения

### <a name="error-message-cannot-open-file-"></a>Сообщение об ошибке: "Не может открыть файл '...'"

Эта ошибка возникает, когда выполнение пакета не может найти файл на локальном диске во время выполнения интеграции SSIS. Попробуйте выполнить следующие действия:
* Не используйте абсолютный путь в пакете, который выполняется во время выполнения интеграции SSIS. Используйте текущий рабочий каталог исполнения (.) или папку temp (%TEMP%) Вместо.
* Если вам необходимо сохранить некоторые файлы на узлах sSIS интеграции времени выполнения, подготовьте файлы, как описано в [настройке customize.](how-to-configure-azure-ssis-ir-custom-setup.md) Все файлы в рабочем каталоге будут очищены после завершения выполнения.
* Используйте файлы Azure вместо хранения файла в узлах времени выполнения интеграции SSIS. Для получения подробной информации [см.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Сообщение об ошибке: "База данных 'SSISDB' достигла своей квоты размера"

Возможная причина заключается в том, что база данных SSISDB, созданная в базе данных SQL Azure или управляемом экземпляре при создании среды выполнения интеграции MSSQL Integration Services, достигла своей квоты. Попробуйте выполнить следующие действия:
* Попробуйте увеличить число единиц транзакций базы данных. См. подробнее об [ограничениях ресурсов для сервера Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Проверьте, создает ли ваш пакет много журналов. В этом случае можно настроить задание обработки эластичных баз данных для очистки этих журналов. См. подробнее об [очистке журналов SSISDB с помощью заданий обработки эластичных баз данных Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Сообщение об ошибке: "Лимит запроса для базы данных ... и была достигнута ".

Если многие пакеты работают параллельно в момент выполнения интеграции SSIS, эта ошибка может произойти из-за того, что SSISDB достиг предела запроса. Рассмотрите возможность увеличения DTC SSISDB для решения этой проблемы. См. подробнее об [ограничениях ресурсов для сервера Базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Сообщение об ошибке: "Операция SSIS не удалась с неожиданным состоянием операции: ..."

Ошибка в основном вызвана переходной проблемой, поэтому постарайтесь повторно запустить выполнение пакета. Примените шаблон повтора в пакете SSIS, выдыхая следующие действия:

* Убедитесь, что пакеты SSIS могут быть перезапущены при сбое без побочных эффектов (например, потеря данных или дублирование данных).
* Наконфигурируете интервал **Retry** и **Retry** активности пакета ![Выполнения **SSIS** на **общей** вкладке.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Для ADO.NET и компонента источника или назначения OLE DB установите **ConnectRetryCount** и **ConnectRetryInterval** в наборе подключения в пакете SSIS или sSIS.

### <a name="error-message-there-is-no-active-worker"></a>Сообщение об ошибке: "Нет активного работника".

Эта ошибка обычно означает, что время выполнения интеграции SSIS имеет неработоспособный статус. Проверьте портал Azure на наличие статуса и подробные ошибки. Для получения дополнительной [Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)информации см.

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Сообщение об ошибке: "Ваше время выполнения интеграции не может быть обновлено и в конечном итоге перестанет работать, так как мы не можем получить доступ к контейнеру Azure Blob, который вы предоставили для пользовательской настройки".

Эта ошибка возникает, когда время выполнения интеграции SSIS не может получить доступ к хранилищу, настроенному для пользовательской настройки. Проверьте, является ли предоставленная вами общая подпись доступа (SAS) действительной и не истекла.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Сообщение об ошибке: "Поставщик услуг анализа Microsoft OLE DB для аналитических служб. 'Hresult: 0x80004005 Описание:' COM ошибка: COM ошибка: mscorlib; Исключение было брошено целью вызова"

Одной из потенциальных причин является то, что имя пользователя или пароль с включенным Azure Multi-Factor Authentication настроены для проверки подлинности Azure Analysis Services. Эта аутентификация не поддерживается во время выполнения интеграции SSIS. Попробуйте использовать принцип службы проверки подлинности Azure Analysis Services:

1. Подготовьте директор службы, как описано в [Автоматизации, с помощью основ службы.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. В интерфейсе Connection Manager нанастройка **Используйте определенное имя пользователя и пароль:** установите **AppID** в качестве имени пользователя и **clientSecret** в качестве пароля.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Сообщение об ошибке: "ADONET Source не удалось приобрести соединение (GUID) со следующим сообщением об ошибке: Войти не удалось для пользователя 'NT AUTHORITY-ANONYMOUS LOGON'" при использовании управляемого удостоверения

Убедитесь, что вы не настроить метод проверки подлинности менеджера соединения как **активная проверка паролем каталога,** когда параметр *ConnectUsingManagedIdentity* **является правдой.** Вместо этого вы можете настроить его в качестве **проверки подлинности S'L,** которая игнорируется при установке *ConnectUsingManagedIdentity.*

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Сообщение об ошибке: "0xC020801F на ..., OData Источник : Не может приобрести управляемое соединение от менеджера соединения времени выполнения"

Одной из потенциальных причин является то, что транспортная безопасность слоя (TLS) не включается в время выполнения интеграции SSIS, что требуется вашим источником OData. Вы можете включить TLS в время выполнения интеграции SSIS с помощью настройки. Более подробную информацию можно найти на [Can't connect Project Online Odata из SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) и [настроить настройки для выполнения интеграции Azure-SSIS.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Сообщение об ошибке: "Запрос постановки задачи с эксплуатацией гид ... сбой, так как ошибка: Не удалось отправить постановку операции с сообщением об ошибке: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Не удалось загрузить прокси данных ".

Убедитесь, что время выполнения интеграции Azure-SSIS настроено с помощью автономного времени выполнения интеграции. Более подробную информацию можно найти на [Configure Self-Hosted IR в качестве прокси для Azure-SSIS IR в ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Сообщение об ошибке: "Статус задачи постановки: Не удалось. Ошибка постановки задачи: Ошибка Кода: 2010, ErrorMessage: Самохонцентричная интеграция Runtime ... находится в автономном режиме"

Убедитесь, что время автономной интеграции установлено и запущено. Более подробную информацию можно найти на [создание и настройка автономной интеграции](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Сообщение об ошибке: "Ошибка задачи: Ошибка: 2906, ErrorMessage: Выполнение пакета не удалось., Выход: "ОперацияОшибоды": "Ошибка: Запрошенный поставщик OLE DB ... не зарегистрирован. Если 64-разрядный драйвер не установлен, запустите пакет в 32-битном режиме..."

Убедитесь, что соответствующий поставщик, используемый разъемами OLE DB в вашем пакете, установлен на самохотваченной интеграционной машине. Более подробную информацию можно найти на [Configure Self-Hosted IR в качестве прокси для Azure-SSIS IR в ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Сообщение об ошибке: "Ошибка задачи: Ошибка: 2906, ErrorMessage: Выполнение пакета не удалось., Выход: "ОперацияОшибодыСообщения": "Ошибка: System.IO.FileLoadException: Не может загрузить файл или сборку 'Microsoft.WindowsAzure.Storage, Версия ..., Культура-нейтральная, PublicKeyToken-31bf3856ad364e35' или один из его зависимостей. Явное определение расположенной сборки не соответствует ссылке сборки. ..."

Одной из потенциальных причин является то, что время автономной интеграции не устанавливается или не обновляется должным образом. Предложите загрузить и переустановить последнюю самохозню время запуска интеграции. Более подробную информацию можно найти на [создание и настройка автономной интеграции](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Сообщение об ошибке: "При запросе метаданных требуется подключение. Если вы работаете в автономном режиме, отключите Work Offline в меню SSIS, чтобы включить подключение"

* Возможная причина и рекомендуемое действие.
  * Если в журнале исполнения также есть предупреждающее сообщение "Компонент не поддерживает использование менеджера соединения с значением ConnectByProxy, устанавливающим истину", это означает, что менеджер соединения используется на компоненте, который еще не поддерживает "ConnectByProxy". Поддерживаемые компоненты можно найти на [Configure Self-Hosted IR в качестве прокси для Azure-SSIS IR в ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Журнал выполнения можно найти в [отчете SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) или в папке журнала, указанной в деятельности выполнения пакета SSIS.
  * vNet также может использоваться для доступа к данным о помещениях в качестве альтернативы. Более подробную информацию можно найти на [момент выполнения интеграции Azure-SSIS в виртуальную сеть](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Сообщение об ошибке: "Статус задачи постановки: Не удалось. Ошибка постановки задачи: Ошибка Code: 2906, ErrorMessage: Выполнение пакета не удалось., Выход: "ОперацияОшибкаСообщения": "SSIS Исполнитель выход код: -1.'n", "LogLocation": "... \\SSISTelemetry\\ExecutionLog\\...", "эффективнаяИнтеграцияRuntime": "...", "executionDuration": ..., "durationInQueue": "интеграцияRuntimeQueue": ...

Убедитесь, что время выполнения Visual C'' установлено на самохотваченной интеграционной интеграционной машине. Более подробную информацию можно найти на [Configure Self-Hosted IR в качестве прокси для Azure-SSIS IR в ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Несколько выполнения пакетов срабатывают неожиданно

* Возможная причина и рекомендуемое действие.
  * Для запуска выполнения пакета SSIS используются действия процедуры ADF. Команда t-sql может поразить переходную проблему и вызвать повтор, что может привести к нескольким выполнениям пакетов.
  * Вместо этого используйте действия ExecuteSSISPackage, которые гарантируют, что выполнение пакета не будет повторено, если не будет учитываться повторная попытка пользователя в действиях. Подробную информацию можно найти по адресу[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Уточните команду t-sql, чтобы иметь возможность повторного запуска, проверяя, если выполнение уже сработало

### <a name="package-execution-takes-too-long"></a>Выполнение пакета занимает слишком много времени

Вот потенциальные причины и рекомендуемые действия:

* Слишком много выполнения пакетов было запланировано на время выполнения интеграции SSIS. Все эти казни будут ждать в очереди на свою очередь.
  * Определите максимум, используя эту формулу:

    Макс Параллельный отсчет исполнения на ИК-счет - Узлы отсчета
  * Чтобы узнать, как установить количество узлов и максимальное параллельное выполнение на узла, [см.](create-azure-ssis-integration-runtime.md)
* Время выполнения интеграции SSIS остановлено или имеет нездоровый статус. Чтобы узнать, как проверить состояние и ошибки [интеграции](monitor-integration-runtime.md#azure-ssis-integration-runtime)SSIS, см.

Мы также рекомендуем установить тайм-аут на ![вкладке](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **«Общий»:** Установите свойства на вкладке «Общий».

### <a name="poor-performance-in-package-execution"></a>Низкая производительность при выполнении пакетов

Попробуйте выполнить следующие действия:

* Убедитесь, что время выполнения интеграции SSIS находится в том же регионе, что и источник данных и пункт назначения.

* Установите уровень выполнения пакета для **выполнения** пакета для сбора информации о продолжительности для каждого компонента в исполнении. Для получения подробной информации [см.](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Проверьте производительность ИК-узлов на портале Azure:
  * Для получения информации о том, как контролировать время выполнения интеграции SSIS, можно узнать о [времени выполнения интеграции Azure-SSIS.](monitor-integration-runtime.md#azure-ssis-integration-runtime)
  * Вы можете найти историю cPU/memory для выполнения интеграции SSIS, просмотрев метрики фабрики данных на портале Azure.
    ![Мониторинг метрик времени выполнения интеграции SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
