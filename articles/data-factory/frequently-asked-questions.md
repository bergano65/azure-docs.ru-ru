---
title: 'Фабрика данных Azure: часто задаваемые вопросы '
description: Получите ответы на часто задаваемые вопросы о фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 8238f2ea8395fc53044703db619d768918cb1834
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644704"
---
# <a name="azure-data-factory-faq"></a>Часто задаваемые вопросы о фабрике данных Azure
Эта статья содержит ответы на часто задаваемые вопросы о фабрике данных Azure.  

## <a name="what-is-azure-data-factory"></a>Что такое фабрика данных Azure? 
Фабрика данных — это полностью управляемая облачная служба ETL с интеграцией данных, которая автоматизирует перемещение и преобразование данных. Как на фабрике сырье превращается в готовую продукцию с помощью оборудования, так и в фабриках данных Azure необработанные данные собираются и преобразовываются в готовые к использованию сведения с помощью специальных служб. 

Фабрика данных Azure позволяет создавать управляемые данными рабочие процессы для перемещения данных между локальными и облачными хранилищами данных. И можно обрабатывать и преобразовывать данные с помощью потоков данных. ADF также поддерживает внешние модули вычислений для ручных преобразований с помощью служб вычислений, таких как Azure HDInsight, Azure Databricks и среды выполнения интеграции SQL Server Integration Services (SSIS). 

С помощью фабрики данных вы можете выполнить обработку данных, используя облачную службу на основе Azure или собственную вычислительную среду с локальным размещением, например SSIS, SQL Server или Oracle. После создания конвейера, который выполняет необходимое действие, можно запланировать его периодическое выполнение (например, ежечасно, ежедневно или еженедельно), Расписание окна времени или запустить конвейер из события. Дополнительную информацию см. в статье [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md).

### <a name="control-flows-and-scale"></a>Потоки управления и масштабирование 
Для поддержки разнообразных потоков интеграции и шаблонов в современном хранилище данных фабрика данных обеспечивает гибкое моделирование конвейера данных. Это влечет за собой парадигмы программирования полного управления, которые включают в себя условное выполнение, ветвление в конвейерах данных и возможность явно передавать параметры внутри и по этим потокам. Поток управления также охватывает преобразование данных посредством отправки действий во внешние ядра выполнения и возможности потока данных, включая перемещение данных в масштабе с помощью действия копирования.

Фабрика данных позволяет создать любой поток для своего сценария интеграции данных и запускать его по запросу или постоянно по расписанию. Ниже приведены несколько общих потоков, которые эта модель поддерживает.   

- Потоки управления:
    - Действия можно объединять в цепочку в конвейере.
    - Действия могут быть разветвлены в конвейере.
    - Параметры
        - Параметры можно определять на уровне конвейера, и аргументы можно передавать при вызове конвейера по требованию или из триггера.
        - Действия могут использовать аргументы, передаваемые в конвейер.
    - Пользовательская передача состояния:
        - Выходные данные действия, включая состояние, могут использоваться последующими действиями в конвейере.
    - Контейнеры циклов:
        - Действие ForEach выполняет итерацию указанной коллекции действий в цикле. 
- Потоки на основе триггеров:
    - Конвейеры можно вызывать по требованию или в определенное время.
- Разностные потоки:
    - Параметры можно использовать для определения отметки высокой нагрузки для разностного копирования при перемещении таблиц измерений или ссылок из реляционного хранилища (локально или в облаке) для загрузки данных в Lake. 

Дополнительные сведения см. в статье [Ветвления и создание цепочки действий в конвейере фабрики данных](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Преобразование данных в масштабе с помощью конвейеров без кода
Новый браузерный инструментарий позволяет писать и развертывать конвейеры без кода с помощью современного интерактивного веб-интерфейса.

Для разработчиков визуальных данных и инженеров по данным веб-интерфейс фабрики данных — это среда проектирования без кода, которая будет использоваться для построения конвейеров. Он полностью интегрирован с Git Visual Studio Online и обеспечивает интеграцию для CI/CD и итеративной разработки с параметрами отладки.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Многоплатформенные пакеты SDK для опытных пользователей
Фабрика данных версии 2 предоставляет обширный набор пакетов SDK, которые можно использовать для создания, управления и мониторинга конвейеров с помощью предпочтительной интегрированной среды разработки, в том числе:
* Пакет Python SDK
* Интерфейс командной строки PowerShell
* Пакет SDK для C#

Пользователи также могут использовать документированные API-интерфейсы RESTFUL для взаимодействия с фабрикой данных версии 2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Итеративная разработка и отладка с помощью визуальных средств
Визуальные инструменты фабрики данных Azure позволяют выполнять итеративную разработку и отладку. Вы можете создавать конвейеры и выполнять тестовые запуски с помощью функции **отладки** на холсте конвейера, не написав ни одной строки кода. Результаты тестовых запусков можно просмотреть в окне **вывод** на холсте конвейера. После выполнения теста можно добавить в конвейер дополнительные действия и продолжить отладку в итеративном режиме. Вы также можете отменить тестовые запуски после выполнения. 

Перед выбором **отладки**вы не обязаны публиковать изменения в службе фабрики данных. Это полезно в тех случаях, когда необходимо убедиться, что новые добавления или изменения будут работать должным образом перед обновлением рабочих процессов фабрики данных в средах разработки, тестирования или рабочей среде. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Возможность развертывания пакетов служб SSIS в Azure 
Если вы хотите перемещать рабочие нагрузки служб SSIS, то создайте фабрику данных и подготовьте среду выполнения интеграции Azure SSIS. Среда выполнения интеграции Azure SSIS — это полностью управляемый кластер виртуальных машин Azure (узлов), выделенных для выполнения пакетов служб SSIS в облаке. Пошаговые инструкции см. в руководстве [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>Пакеты SDK
Если вы являетесь опытным пользователем и ищете программный интерфейс, фабрика данных предоставляет обширный набор пакетов SDK, которые можно использовать для создания, управления и мониторинга конвейеров с помощью любимой интегрированной среды разработки. Поддерживаются такие языки, как .NET, Python, PowerShell и REST.

### <a name="monitoring"></a>Мониторинг
Фабрики данных можно отслеживать с помощью PowerShell, пакета SDK и визуальных средств наблюдения в браузерном пользовательском интерфейсе. Вы можете выполнять мониторинг и управление настраиваемыми потоками по запросу, на основе триггеров и с часами, используя эффективный и эффективный способ. Отмена существующих задач, подробное описание сбоев, детализация для получения подробных сообщений об ошибках и Отладка проблем с одной панелью стекла без переключения контекста или переходом между экранами. 

### <a name="new-features-for-ssis-in-data-factory"></a>Новые возможности служб SSIS в фабрике данных
С момента первоначального выпуска общедоступной предварительной версии в 2017 фабрика данных добавила следующие функции для служб SSIS:

-   Поддержка трех дополнительных конфигураций и вариантов базы данных SQL Azure для размещения базы данных служб SSIS (SSISDB) проектов и пакетов.
-   База данных SQL с конечными точками службы виртуальной сети
-   Управляемый экземпляр
-   Эластичный пул
-   Поддержка Azure Resource Manager виртуальной сети поверх классической виртуальной сети будет считаться устаревшей в будущем, что позволяет внедрить или присоединить среду выполнения интеграции Azure SSIS к виртуальной сети, настроенной для базы данных SQL, с конечными точками службы виртуальной сети, а также с доступом к локальным данным. Дополнительные сведения см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md).
-   Поддержка проверки подлинности Azure Active Directory (Azure AD) и аутентификации SQL для подключения к SSISDB, что позволяет выполнять аутентификацию Azure AD с помощью управляемого удостоверения фабрики данных для ресурсов Azure.
-   Поддержка приведения локальной SQL Server лицензии для получения значительной экономии средств Преимущество гибридного использования Azure
-   Поддержка Enterprise Edition среды выполнения интеграции Azure SSIS, которая позволяет использовать расширенные функции и возможности расширенного пользовательского интерфейса установки для установки дополнительных компонентов и расширений, а также экосистемы партнеров. Дополнительные сведения см. в статьях [выпуск Enterprise, Выборочная установка и сторонние расширения для служб SSIS в ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Более глубокая интеграция служб SSIS в фабрике данных, которая позволяет вызывать и активировать действия первого класса для выполнения пакетов служб SSIS в конвейерах фабрики данных и планировать их с помощью SSMS. Дополнительные сведения см. в разделе также [модернизировать и расширьте рабочие процессы ETL/ELT с помощью действий служб SSIS в конвейерах ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Что такое среда выполнения интеграции?
Среда выполнения интеграции — это вычислительная инфраструктура, которую фабрика данных Azure использует для предоставления следующих возможностей интеграции данных в различных сетевых средах:

- **Перемещение данных**. для перемещения данных среда выполнения интеграции перемещает данные между исходным и целевым хранилищами данных, обеспечивая поддержку встроенных соединителей, преобразования формата, сопоставления столбцов, а также высокопроизводительной и масштабируемой передачи данных.
- **Действия диспетчеризации**. для преобразования среда выполнения интеграции предоставляет возможности для встроенного выполнения пакетов служб SSIS.
- **Выполнение пакетов служб SSIS**. Среда выполнения интеграции изначально выполняет пакеты служб SSIS в управляемой среде вычислений Azure. Среда выполнения интеграции также поддерживает действия по преобразованию и мониторингу, выполняемые в различных службах вычислений, таких как Azure HDInsight, Машинное обучение Azure, база данных SQL и SQL Server.

Можно развернуть один или несколько экземпляров среды выполнения интеграции в соответствии с требованиями для перемещения и преобразования данных. Среда выполнения интеграции может выполняться в общедоступной сети Azure или в частной сети (в локальной среде, в виртуальной сети Azure или Amazon Web Services виртуальном частном облаке [VPC]). 

Дополнительные сведения см. в статье [Среда выполнения интеграции в фабрике данных Azure](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Что такое ограничение количества сред выполнения интеграции?
В фабрике данных нет жестких ограничений на количество экземпляров среды выполнения интеграции. Однако есть ограничение на число ядер виртуальной машины, которые среда выполнения интеграции может использовать для каждой подписки при выполнении пакетов служб SSIS. Дополнительные сведения см. в разделе [Ограничения фабрики данных](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Какие основные концепции в фабрике данных Azure?
В подписке Azure может быть один или несколько экземпляров фабрики данных Azure. Фабрика данных Azure содержит четыре ключевых компонента. Они образуют платформу, на которой можно создавать управляемые данными рабочие процессы, предусматривающие перемещение и преобразование данных.

### <a name="pipelines"></a>Конвейеры
Фабрика данных может иметь один или несколько конвейеров. Конвейер — это логическая группа действий, которые выполняют определенный блок задач. Действия в конвейере совместно выполняют задачу. Например, конвейер может включать группу действий, которые принимают данные из большого двоичного объекта Azure и выполняют запрос Hive в кластере HDInsight для секционирования данных. Преимуществом является то, что конвейер позволяет управлять группами действий, а не каждым отдельным действием. Вы можете связать вместе действия в конвейере, чтобы выполнять их последовательно, или выполнять их параллельно и независимо друг от друга.

### <a name="data-flows"></a>Потоки данных
Потоки данных — это объекты, создаваемые визуально в фабрике данных, которые преобразуют данные при масштабировании в серверных службах Spark. Нет необходимости понимать внутренние компоненты программирования или Spark. Просто Разработайте цель преобразования данных с помощью графов (сопоставлений) или электронных таблиц (структурирование).

### <a name="activities"></a>Действия
Действия представляют отдельные этапы обработки в конвейере. Например, действие копирования можно использовать для копирования данных из одного хранилища данных в другое. Точно так же можно использовать действие Hive, которое выполняет запрос Hive к кластеру Azure HDInsight для преобразования или анализа данных. Фабрика данных поддерживает три типа действий: действия перемещения данных, действия преобразования данных и действия управления.

### <a name="datasets"></a>Наборы данных
Наборы данных представляют структуры данных в хранилищах. Эти структуры указывают данные, необходимые для использования в действиях, разделяя их на входные и выходные. 

### <a name="linked-services"></a>Связанные службы
Связанные службы напоминают строки подключения, определяющие сведения о подключении, необходимые для подключения фабрики данных к внешним ресурсам. Обдумайте это таким образом: связанная служба определяет соединение с источником данных, а набор данных представляет структуру. Например, связанная служба хранилища Azure определяет строку подключения для подключения к учетной записи хранения Azure. И набор данных BLOB-объектов Azure указывает контейнер больших двоичных объектов и папку, содержащую данные.

Связанные службы используются в фабрике данных для двух целей:

- Для представления *хранилища данных*, включая, помимо прочего, локальный экземпляр SQL Server, экземпляр базы данных Oracle, общую папку и учетную запись хранилища BLOB-объектов Azure. Список поддерживаемых хранилищ данных см. в статье [Действие копирования в фабрике данных Azure](copy-activity-overview.md).
- Для представления *вычислительного ресурса*, в котором можно выполнить действие. Например, действие HDInsightHive выполняется в кластере Hadoop в HDInsight. Список поддерживаемых действий преобразования и вычислительных сред см. в статье [Преобразование данных в фабрике данных Azure](transform-data.md).

### <a name="triggers"></a>Триггеры
Триггеры обозначают единицу обработки, которая определяет время запуска для выполнения конвейера. Существует несколько типов триггеров для разных событий. 

### <a name="pipeline-runs"></a>Запуски конвейера
Запуск конвейера — это экземпляр выполнения конвейера. Запуск конвейера обычно выполняется путем передачи аргументов для параметров, определенных в конвейере. Вы можете передать аргументы вручную или в определении триггера.

### <a name="parameters"></a>Параметры
Параметры представляют собой пары "ключ — значение" в конфигурации только для чтения. Вы определяете параметры в конвейере и передаете аргументы для определенных параметров во время выполнения из контекста выполнения. Контекст запуска создается триггером или из конвейера, который выполняется вручную. Действия в конвейере используют значения параметров.

Набор данных — это строго типизированный параметр и сущность, которую можно повторно использовать или ссылаться на них. Действие может ссылаться на наборы данных и может использовать свойства, определенные в определении DataSet.

Связанная служба также является строго типизированным параметром, который содержит сведения о подключении к хранилищу данных или среде вычислений. Она также является сущностью, которую можно повторно использовать или ссылаться на нее.

### <a name="control-flows"></a>Потоки управления
Потоки управления выполняют оркестрацию действий в конвейере, которая включает цепочки действий в последовательности, ветвление и параметры, определяемые на уровне конвейера, а также аргументы, которые передаются во время вызова конвейера по запросу или из триггера. Потоки управления также включают в себя пользовательскую передачу состояния и контейнеры циклов (то есть итераторы foreach).


Дополнительные сведения о понятиях фабрики данных см. в следующих статьях:

- [Datasets and linked services in Azure Data Factory](concepts-datasets-linked-services.md) (Наборы данных и связанные службы в фабрике данных Azure)
- [Конвейеры и действия](concepts-pipelines-activities.md)
- [Среда выполнения интеграции в фабрике данных Azure](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Какая модель ценообразования применяется для фабрики данных?
Подробные сведения о ценах на фабрику данных Azure см. на [этой странице](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Как оставаться в курсе последних новостей о фабрике данных?
Чтобы узнавать о последних новостях о фабрике данных Azure, используйте следующие сайты:

- [Блог](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Документация по фабрике данных Azure V2](/azure/data-factory)
- [Домашняя страница продукта](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Подробное техническое руководство 

### <a name="how-can-i-schedule-a-pipeline"></a>Как запланировать конвейер? 
Для планирования конвейера можно использовать триггер планировщика или триггер по временному окну. Триггер использует расписание "Календарь на стене", которое может периодически планировать конвейеры или в шаблонах, основанных на календарях (например, в понедельниках 6:00 PM и четверг в 9:00 РМ). Дополнительные сведения см. в статье [Выполнение конвейера и триггеры в фабрике данных Azure](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Можно ли передать параметры в выполнение конвейера?
Да, параметры являются первым классом, концепцией верхнего уровня в фабрике данных. Вы можете определить параметры на уровне конвейера и передать аргументы при выполнении конвейера, запускаемого по требованию или с помощью триггера.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Можно ли определить значения по умолчанию для параметров конвейера? 
Да. Вы можете определить значения по умолчанию для параметров в конвейерах. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Может ли действие в конвейере использовать аргументы, передаваемые в конвейер? 
Да. Каждое действие в рамках конвейера может использовать значение параметра, переданное в конвейер и запущенное с помощью конструкции `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Может ли свойство из выходных данных действия использоваться в другом действии? 
Да. Выходные данные действия могут использоваться в последующем действии. Для этого применяется конструкция `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Как корректно обрабатывать значения null в выходных данных действия? 
Для корректной обработки значений null в выражениях можно использовать конструкцию `@coalesce`. 

## <a name="mapping-data-flows"></a>Сопоставление потоков данных

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Мне нужна помощь в устранении неполадок с логикой потока данных. Какие сведения необходимо предоставить для получения помощи?

Если корпорация Майкрософт предоставляет справку или устранение неполадок с потоками данных, укажите сценарий потока данных. Это скрипт кода программной части из графа потока данных. В пользовательском интерфейсе ADF Откройте поток данных, а затем нажмите кнопку "Скрипт" в правом верхнем углу. Скопируйте и вставьте этот скрипт или сохраните его в текстовом файле.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Разделы справки получить доступ к данным с помощью других типов наборов данных 90 в фабрике данных?

В настоящее время функция потока данных сопоставления разрешает базу данных SQL Azure, хранилище данных SQL Azure, текстовые файлы с разделителями из хранилища BLOB-объектов Azure или Azure Data Lake Storage 2-го поколения, а также файлы Parquet из хранилища BLOB-объектов или Data Lake Storage 2-го поколения собственными для источника и приемника. 

Используйте действие копирования для размещения данных из других соединителей, а затем выполните действие потока данных для преобразования данных после их промежуточного размещения. Например, конвейер сначала будет копировать в хранилище BLOB-объектов, а затем действие потока данных будет использовать набор данных в источнике для преобразования этих данных.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Доступна ли локальная среда выполнения интеграции для потоков данных?

Локальная IR — это конструкция конвейера ADF, которую можно использовать с действием копирования для получения или перемещения данных в локальные или источники данных и приемники, основанные на виртуальной машине. Сначала создайте данные с помощью копии, затем потока данных для преобразования, а затем последующей копии, если необходимо переместить преобразованные данные обратно в локальное хранилище.

## <a name="wrangling-data-flows"></a>Потоки данных структурирование

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Какие регионы поддерживаются для потока данных структурирование?

Поток данных структурирование в настоящее время поддерживается в фабриках данных, созданных в следующих регионах:

* Восточная Австралия
* Центральная Канада
* Центральная Индия
* Центральная часть США
* Восточная часть США
* Восточная часть США 2
* Восточная Япония
* Северная Европа
* Юго-Восточная Азия
* Центрально-южная часть США
* Южная часть Соединенного Королевства
* Центрально-западная часть США
* Западная Европа
* Западная часть США
* Западная часть США 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Каковы ограничения и ограничения для потока данных структурирование?

Имена наборов данных могут содержать только буквенно-цифровые символы. Поддерживаются следующие хранилища данных:

* Набор данных DelimitedText в хранилище BLOB-объектов Azure с помощью проверки подлинности ключа
* Набор данных DelimitedText в Azure Data Lake Storage Gen2 с использованием проверки подлинности ключа учетной записи или субъекта-службы
* Набор данных DelimitedText в Azure Data Lake Storage Gen1 с использованием проверки подлинности субъекта-службы
* База данных SQL Azure и хранилище данных с проверкой подлинности SQL. См. Поддерживаемые типы SQL ниже. Отсутствует поддержка Polybase или промежуточного хранения для хранилища данных.

В настоящее время интеграция Key Vault связанных служб не поддерживается в потоках данных структурирование.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>В чем разница между потоками сопоставления и структурирование данных?

Сопоставление потоков данных предоставляет способ преобразования данных в масштабе без необходимости написания кода. Можно спроектировать задание преобразования данных на холсте потока данных, создав ряд преобразований. Начните с произвольным количеством исходных преобразований, за которыми следуют шаги преобразования данных. Завершите поток данных с помощью приемника, чтобы получить результаты в месте назначения. Поток данных сопоставления отлично подходит для сопоставления и преобразования данных с известными и неизвестными схемами в приемниках и источниках.

Потоки данных структурирование позволяют выполнять динамическую подготовку и исследование данных с помощью интерактивного редактора гибридных веб-приложений Power Query в режиме масштабирования с помощью выполнения Spark. В озера данных иногда нужно только исследовать набор данных или создать набор данных в Lake. Вы не сопоставляется с известным целевым объектом. Потоки данных структурирование используются для менее формальных и основанных на моделях аналитических сценариев.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>В чем разница между потоками данных в платформе Power Platform и структурирование данными?

Потоки данных платформы питания позволяют пользователям импортировать и преобразовывать данные из широкого диапазона источников в Common Data Service и Azure Data Lake для создания приложений PowerApps, Power BI отчетов или автоматизации потоков. Потоки данных на платформе питания используют установленную Power Query подготовки к работе с данными, как Power BI и Excel. Потоки данных платформы питания также обеспечивают простое повторное использование в Организации и автоматически управляют согласованием (например, автоматическое обновление потоков данных, зависящих от другого потока данных при обновлении предыдущего).

Фабрика данных Azure (ADF) — это управляемая служба интеграции данных, которая позволяет специалистам по управлению данными и составным интеграторам данных создавать сложные гибридные рабочие процессы извлечения-преобразования, загрузки (ETL) и извлечения-загрузки (ELT). Поток данных структурирование в ADF предоставляет пользователям бессерверную среду, которая упрощает подготовку данных в облаке и масштабируется до любого размера данных без необходимости управления инфраструктурой. Для подготовки и формирования данных в нем используется технология подготовки данных Power Query (также используемая в потоках управления питанием на платформе питания, Excel и Power BI). Структурирование потоки данных позволяют пользователям быстро подготавливать данные в масштабе с помощью выполнения Spark, так как в нем реализованы все сложности и возможности масштабирования, связанные с интеграцией больших данных. Пользователи могут создавать отказоустойчивые конвейеры данных в доступной визуальной среде с помощью нашего интерфейса на основе браузера и передавать ADF-файлам сложности выполнения Spark. Создавайте расписания для конвейеров и отслеживайте выполнение потока данных на портале мониторинга ADF. Легко управляйте соглашениями об уровне обслуживания данных с богатыми возможностями мониторинга доступности ADF и оповещениями и используйте встроенные возможности непрерывной интеграции и развертывания для сохранения и управления потоками в управляемой среде. Создавайте оповещения и просматривайте планы выполнения, чтобы проверить, что логика выполняется в соответствии с запланированной настройкой потоков данных.

### <a name="supported-sql-types"></a>Поддерживаемые типы SQL

Поток данных структурирование поддерживает следующие типы данных в SQL. При использовании неподдерживаемого типа данных возникнет ошибка проверки.

* short
* double
* real
* FLOAT
* char
* nchar
* varchar
* nvarchar
* integer
* int
* bit
* Логическое
* smallint
* tinyint
* BIGINT
* long
* text
* Дата
* DATETIME
* datetime2
* smalldatetime
* TIMESTAMP
* UNIQUEIDENTIFIER
* Xml

В будущем будут поддерживаться и другие типы данных.

## <a name="next-steps"></a>Дальнейшие действия
Пошаговые инструкции по созданию фабрики данных см. в следующих руководствах:

- [Создание фабрики данных и конвейера с помощью пакета SDK .NET](quickstart-create-data-factory-dot-net.md)
- [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью фабрики данных Azure](tutorial-copy-data-dot-net.md)
