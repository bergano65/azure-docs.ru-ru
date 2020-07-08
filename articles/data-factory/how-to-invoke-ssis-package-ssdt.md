---
title: Выполнение пакетов служб SSIS из SSDT
description: Узнайте, как выполнять пакеты служб SSIS в Azure из SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 1d8261d05f59c7f40ba6b1e2d59d2b15ad56de95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424588"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Выполнение пакетов служб SSIS в Azure из SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается функция проектов SQL Server Integration Services (SSIS) с поддержкой Azure в SQL Server Data Tools (SSDT), которая позволяет запускать пакеты на Azure-SSIS Integration Runtime (IR) в фабрике данных Azure (ADF).  Эту функцию можно использовать для тестирования существующих пакетов служб SSIS, прежде чем претянуть & Shift/перенести их в Azure или разрабатывать новые пакеты служб SSIS для работы в Azure.

С помощью этой функции можно создать новый Azure-SSIS IR или присоединить существующий объект к проектам служб SSIS, а затем выполнить на нем пакеты.  Мы поддерживаем запуск пакетов для развертывания в каталоге служб SSIS (SSISDB) в модели развертывания проекта, а также для развертывания в файловых системах, файловых ресурсах или файлах Azure в модели развертывания пакетов. 

## <a name="prerequisites"></a>Предварительные условия
Чтобы использовать эту функцию, скачайте и установите последнюю версию SSDT с расширением проектов SSIS для Visual Studio отсюда или как автономный [установщик отсюда.](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) [here](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Включение проектов служб SSIS в Azure
### <a name="create-new-azure-enabled-ssis-projects"></a>Создание новых проектов служб SSIS с поддержкой Azure
В SSDT можно создать новые проекты служб SSIS с поддержкой Azure с помощью шаблона **Integration Services проекта (Azure с возможностью использования)** .

   ![Новый проект служб SSIS с поддержкой Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

После создания проекта с поддержкой Azure вам будет предложено подключиться к службам SSIS в фабрике данных Azure.

   ![Запрос Azure-SSIS IR подключения](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Если вы хотите немедленно подключиться к Azure-SSIS IR, см. Дополнительные сведения в разделе [Подключение к Azure-SSIS IR](#irconnect) . Вы также можете подключиться позже, щелкнув правой кнопкой мыши узел проекта на обозреватель решений панели SSDT, чтобы открыть меню и выбрать пункт меню **Подключение к службам SSIS в фабрике данных Azure** в подменю **службы SSIS в фабрике данных Azure** .

### <a name="azure-enable-existing-ssis-projects"></a>Azure — включение существующих проектов служб SSIS
Для существующих проектов служб SSIS их можно включить в Azure, выполнив следующие действия.

1. Щелкните правой кнопкой мыши узел проекта на панели обозреватель решений SSDT, чтобы открыть меню, а затем выберите пункт меню **проект с поддержкой Azure** в подменю **службы SSIS в фабрике данных Azure** , чтобы запустить **Мастер проектов с поддержкой Azure**.

   ![Azure — Включение существующего проекта служб SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. На странице **Выбор конфигурации Visual Studio** выберите конфигурацию Visual Studio, чтобы применить параметры выполнения пакета в Azure. Рекомендуется создать новую конфигурацию Visual Studio для облака и Azure, чтобы обеспечить работу вашего проекта в конфигурации облака. При наличии нескольких конфигураций можно назначить разные значения для параметров на основе различных сред (локально или в Azure). Дополнительные сведения см. в [этом примере](#example) .

   ![Выберите конфигурацию Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Для включения в Azure существующих проектов служб SSIS необходимо задать версию целевого сервера, которая будет последней версией, поддерживаемой Azure-SSIS IR, которая в настоящее время **SQL Server 2017**. Если это еще не сделано, необходимо проверить, содержит ли ваш пакет дополнительные компоненты, которые не поддерживаются в SQL Server 2017, и нажать кнопку Далее, чтобы продолжить, если нет никаких проблем.

   ![Переключение версии целевого сервера](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Дополнительные сведения о подключении к Azure-SSIS IR см. в статье [Подключение к Azure-SSIS IR](#irconnect) .

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Подключение проектов с поддержкой Azure к службам SSIS в фабрике данных Azure

Подключив проекты с поддержкой Azure к службам SSIS в ADF, вы можете передать пакеты в службу файлов Azure и запускать их на Azure-SSIS IR. Для этого выполните следующие действия.

1. На странице **Общие сведения о службах SSIS в ADF** ознакомьтесь с введением и нажмите кнопку **Далее** , чтобы продолжить.

   ![Общие сведения о службах SSIS в ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. На странице **Выбор среды IR в ADF** выберите существующий ADF-файл и Azure-SSIS IR для запуска пакетов или создайте новые, если их нет.
   - Чтобы выбрать существующий Azure-SSIS IR, сначала выберите соответствующую подписку Azure и ADF.
   - Если вы выбрали существующий ADF-файл без Azure-SSIS IR, нажмите кнопку **создать службы SSIS IR** , чтобы создать новый на портале или приложении ADF.
   - Если вы выберете существующую подписку Azure, в которой нет ADF, нажмите кнопку **CREATE SSIS IR (создание** среды выполнения интеграции), чтобы запустить **Мастер создания Integration Runtime**, где можно ввести расположение и префикс для автоматического создания новой группы ресурсов Azure, фабрики данных и служб SSIS IR от вашего имени в следующем формате: **йоурпрефикс-RG/DF/IR-йоуркреатионтиме**.

   ![Выбор служб SSIS IR в ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. На странице **Выбор хранилища Azure** выберите существующую учетную запись хранения Azure для отправки пакетов в службу файлов Azure или создайте новую, если у вас нет.
   - Чтобы выбрать существующую учетную запись хранения Azure, сначала выберите соответствующую подписку Azure.
   - Если вы выбрали ту же подписку Azure, что и Azure-SSIS IR, в которой нет учетной записи хранения Azure, нажмите кнопку " **создать службу хранилища Azure** ", чтобы автоматически создать новое имя от вашего имени в том же расположении, где находится ваша Azure-SSIS IR, с именем, объединив префикс имени Azure-SSIS IR и даты его создания.
   - Если вы выбрали другую подписку Azure без учетной записи хранения Azure, нажмите кнопку " **создать хранилище Azure** ", чтобы создать ее на портал Azure.

   ![Выбор службы хранилища Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Нажмите кнопку **подключить** , чтобы завершить подключение.  Выбранные Azure-SSIS IR и учетная запись хранения Azure будут отображаться в узле **связанные ресурсы Azure** на панели Обозреватель решений SSDT.  Мы также обновляем состояние Azure-SSIS IR, а вы можете управлять им, щелкнув его узел правой кнопкой мыши, чтобы открыть меню, а затем выбрав пункт меню **старт\стоп\манаже** , который переводит вас на портал или приложение ADF.

## <a name="execute-ssis-packages-in-azure"></a>Выполнение пакетов служб SSIS в Azure
### <a name="azure-enabled-setting"></a>Параметр с поддержкой Azure
Перед выполнением пакетов в Azure можно выбрать настройку параметров выполнения. Если вы хотите включить проверку подлинности Windows для пакетов служб SSIS, выполните следующие действия.

1. Щелкните правой кнопкой мыши узел проекта на панели обозреватель решений SSDT, чтобы открыть меню, а затем выберите пункт меню **Параметры Azure Enabled** в подменю **службы SSIS в фабрике данных Azure** .

   ![Параметры, поддерживающие Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Щелкните раскрывающийся список **включить проверку подлинности Windows** и выберите **значение true**. Затем нажмите кнопку изменить для параметра **учетные данные проверки подлинности Windows** , чтобы ввести учетные данные.

   ![Включить проверку подлинности Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Укажите учетные данные в редакторе **учетных данных для проверки подлинности Windows** .

   ![Учетные данные проверки подлинности Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Запуск выполнения пакетов
После подключения проектов к службам SSIS в ADF можно выполнять пакеты на Azure-SSIS IR.  Существует два варианта запуска выполнения пакета:
-  Нажмите кнопку **Пуск** на панели инструментов SSDT, чтобы раскрывающийся список и выбрать пункт меню **выполнить в Azure** . 

   ![Выполнение в Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Щелкните правой кнопкой мыши узел пакета на панели обозреватель решений SSDT, чтобы открыть меню и выбрать пункт **выполнить пакет в Azure** .

   ![Выполнение пакета в Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Для выполнения пакетов в Azure требуется запущенная Azure-SSIS IR, поэтому если Azure-SSIS IR остановлена, откроется диалоговое окно для запуска.  За исключением любых пользовательских настроек, этот процесс должен выполняться в течение 5 минут, но для Azure-SSIS IR присоединения к виртуальной сети может потребоваться около 20-30 минут.  После выполнения пакетов в Azure можно запретить Azure-SSIS IR для управления затратами на выполнение, щелкнув правой кнопкой мыши узел на обозреватель решений панели в SSDT, чтобы открыть меню, а затем выбрать пункт меню **старт\стоп\манаже** , чтобы сделать это на портале или приложении ADF.

### <a name="checking-package-execution-logs"></a>Проверка журналов выполнения пакетов
При запуске пакета выполняется форматирование и отображение журнала в окне хода выполнения SSDT.  Для долго выполняющегося пакета мы периодически будем обновлять свой журнал с учетом минут.  Вы можете остановить выполнение пакета, нажав кнопку **остановить** на панели инструментов SSDT, которая немедленно отменит ее.  Кроме того, можно временно найти необработанные данные журнала в пути UNC: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , но мы будем очищать его через один день.

### <a name="switching-package-protection-level"></a>Переключение уровня защиты пакета
Исполнение пакетов служб SSIS в Azure не поддерживает **EncryptSensitiveWithUserKey** / уровни защиты EncryptSensitiveWithUserKey**EncryptAllWithUserKey** .  Следовательно, если вы настроили пакеты с их помощью, мы временно переключим их в **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, используя случайные пароли при передаче пакетов в службу файлов Azure для выполнения на Azure-SSIS IR.

> [!NOTE]
> Если пакеты содержат задачи «Выполнение пакета», которые ссылаются на другие пакеты, настроенные с помощью уровней защиты **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , необходимо вручную перенастроить эти пакеты, чтобы использовать **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, соответственно, перед выполнением пакетов.

Если в пакетах уже настроены **EncryptSensitiveWithPassword** / уровни защиты EncryptSensitiveWithPassword**EncryptAllWithPassword** , мы будем не изменять их, но при передаче пакетов в службу файлов Azure для выполнения на Azure-SSIS IR будут продолжать использовать созданные случайным образом пароли.

### <a name="using-package-configuration-file"></a>Использование файла конфигурации пакета
При использовании файлов конфигурации пакетов в модели развертывания пакетов для изменения значений переменных во время выполнения мы автоматически загружаем эти файлы в файлы Azure для выполнения на Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Использование задачи «Выполнение пакета»
Если пакеты содержат задачи «Выполнение пакета», которые ссылаются на другие пакеты, хранящиеся в локальных файловых системах, необходимо выполнить следующие дополнительные настройки.

1. Отправьте другие пакеты в службу файлов Azure в той же учетной записи хранения Azure, которая подключена к проектам, и получите новый UNC-путь, например`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Замените пути к файлам этих пакетов в диспетчере подключения файлов для задач «Выполнение пакета» на новый UNC-путь.
   - Если компьютер, на котором работает SSDT, не может получить доступ к новому UNC-пути, можно изменить путь к файлу на панели свойств диспетчера подключения файлов.
   - Кроме того, можно использовать переменную для пути к файлу, чтобы назначить правильное значение во время выполнения.

Если пакеты содержат задачи «Выполнение пакета», которые ссылаются на другие пакеты в том же проекте, дополнительная настройка не требуется.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Переключение сред выполнения пакетов с помощью проектов с поддержкой Azure

Чтобы переключить среды выполнения пакетов с проектами с поддержкой Azure, можно создать несколько конфигураций Visual Studio, чтобы применить разные значения для параметров среды. Например, существует простой пакет служб SSIS с **задачей «файловая система** », который устанавливает атрибуты указанного файла. Вы можете просто перенести его в облако, выполнив следующие действия:

1. Определите параметр **FilePath** типа String, который является путем к файлу целевого объекта.

   ![Определение параметра пакета](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Свяжите **соединение с источником** с этим параметром. 

   ![Обновить подключение к источнику](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Создайте новую конфигурацию Visual Studio для облака в Visual Studio Configuration Manager.

4. Определите значения для этого параметра в каждой конфигурации Visual Studio.

   ![Переопределить значения параметров](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure — этот проект служб SSIS включен в конфигурацию Visual Studio для облака.

6. Выполните этот пакет в Azure. Вы можете легко переключиться на локальную среду, переключив текущую конфигурацию Visual Studio.

   ![Переключение конфигурации Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="next-steps"></a>Дальнейшие шаги
Когда вы удовлетворены выполнением пакетов в Azure из SSDT, вы можете развернуть и запустить их как выполнение действий пакета служб SSIS в конвейерах ADF. см. раздел [Запуск пакетов служб SSIS как выполнение действий пакета SSIS в конвейерах ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
