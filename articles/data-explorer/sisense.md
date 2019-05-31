---
title: Визуализация данных с помощью обозревателя данных Azure с помощью Sisense
description: В этой статье Узнайте, как настроить обозреватель данных Azure как источник данных для Sisense и визуализации данных.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358188"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Визуализация данных с помощью обозревателя данных Azure в Sisense

Sisense — это платформа аналитики бизнес-аналитики позволяет создавать приложения analytics, которые обеспечивают высоко интерактивных пользовательских интерфейсов. Бизнес-аналитики и мониторинга, отчетов программного обеспечения позволяет получить доступ к и объединять данные несколькими щелчками мыши. Можно подключиться к данным структурированных и неструктурированных источников, соединять таблицы из нескольких источников с минимальными сценариев и программирования и создавать интерактивные веб-панелей мониторинга и отчеты. В этой статье вы узнаете, как настроить обозреватель данных Azure как источник данных для Sisense и визуализации данных из примера кластера.

## <a name="prerequisites"></a>Технические условия

Вам потребуется для работы с этой статьей:

* [Загрузить и установить приложение Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Создание кластера и базы данных, включающая примеры данных StormEvents. Дополнительные сведения см. в разделе [ Создание кластера и базы данных обозревателя данных Azure](create-cluster-database-portal.md) и [Прием демонстрационных данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Подключение к Sisense панелей мониторинга, с помощью соединителя JDBC обозреватель данных Azure

1. Скачайте и скопируйте последние версии следующих JAR-файлов для *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * 1.1.jar активации
    * adal4j 1.6.0.jar
    * Commons кодек 1.10.jar
    * Commons-collections4-4.1.jar
    * Commons-lang3-3.5.jar
    * gson 2.8.0.jar
    * jcip заметок-1.0-1.jar
    * JSON смарт 1.3.1.jar
    * lang тега 1.4.4.jar
    * 1.4.7.jar электронной почты
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Откройте **Sisense** приложения.
1. Выберите **данных** и выберите **+ ElastiCube** для создания новой модели ElastiCube.
    
    ![Выберите ElastiCube](media/sisense/data-select-elasticube.png)

1. В **добавить новую модель ElastiCube**, назовите модель ElastiCube и **Сохранить**.
   
    ![Добавить новую модель ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Выберите **+ данные**.

    ![Нажмите кнопку "данные"](media/sisense/select-data.png)

1. В **выберите соединитель** выберите **универсального JDBC** соединителя.

    ![Выберите соединитель JDBC](media/sisense/select-connector.png)

1. В **Connect** вкладку, заполните следующие поля для **универсального JDBC** соединитель и выберите **Далее**.

    ![Параметры соединителя JDBC](media/sisense/jdbc-connector.png)

    |Поле |Описание |
    |---------|---------|
    |Строка подключения     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Папку JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Имя класса драйвера    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Имя пользователя   |    Имя пользователя AAD     |
    |Пароль     |   Пароль пользователя AAD      |

1. В **Выбор данных** вкладке, поиска **Выбор базы данных** для выбора соответствующей базы данных, к которому имеются разрешения. В этом примере выберите *test1*.

    ![Выбор базы данных](media/sisense/select-database.png)

1. В *тестирования* области (имя базы данных):
    1. Выберите имя таблицы для предварительного просмотра таблицы и имена столбцов таблицы см. в разделе. Вы можете удалить ненужные столбцы.
    1. Установите флажок, соответствующий таблицы для выбора этой таблицы. 
    1. Нажмите кнопку **Готово**.

    ![Выберите таблицу](media/sisense/select-table-see-columns.png)    

1. Выберите **построения** для создания набора данных. 

    * В **построения** выберите **построения**.

      ![Создание окна](media/sisense/build-window.png)

    * Подождите, пока процесс сборки являются полными и затем выберите **сборка успешно завершена**.

      ![Сборка успешно завершена](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Создание панелей мониторинга Sisense

1. В **Analytics** выберите **+**  >  **новая панель мониторинга** для создания панелей мониторинга для этого набора данных.

    ![Новая панель мониторинга](media/sisense/new-dashboard.png)

1. Выберите информационную панель и выберите **создать**. 

    ![Создание панели мониторинга](media/sisense/create-dashboard.png)

1. В разделе **новое мини-приложение**выберите **+ Выбор данных** для создания новых мини-приложения. 

    ![Добавление полей на панели мониторинга StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Выберите **+ добавить более данных** для добавления дополнительных столбцов в граф. 

    ![Добавить данные графа](media/sisense/add-more-data.png)

1. Выберите **+ мини-приложение** для создания других мини-приложениях. Перетащите мини-приложения для изменения порядка информационной панели.

    ![Панель мониторинга Storm](media/sisense/final-dashboard.png)

Теперь можно исследовать данные с помощью визуализации аналитических, создание дополнительных панелей мониторинга и преобразуйте данные в ценную практическую действенные на ваш бизнес.

## <a name="next-steps"></a>Дальнейшие действия

* [Написание запросов для обозревателя данных Azure](write-queries.md)

