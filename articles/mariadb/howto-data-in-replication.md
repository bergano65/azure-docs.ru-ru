---
title: Настройка репликации данных в базе данных Azure для MariaDB | Документация Майкрософт
description: В этой статье описывается настройка данных в репликации в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444802"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Настройка репликации данных в базе данных Azure для MariaDB

В этой статье описывается настройка данных в репликации в базе данных Azure для MariaDB, настроив сервером главный и реплика. В этой статье предполагается, что у вас есть опыт с MariaDB серверов и баз данных.

Создание реплики в базе данных Azure для службы MariaDB, репликации данных в синхронизирует данные из главного MariaDB на локальном сервере в виртуальных машинах (ВМ), или в облачных службах базы данных.

> [!NOTE]
> Если главный сервер версии 10,2 или более поздней версии, мы рекомендуем настроить репликацию данных в систему с помощью [глобальный идентификатор транзакции](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Создание сервера MariaDB для использования в качестве реплики

1. Создайте новую базу данных Azure для MariaDB сервера (например, replica.mariadb.database.azure.com). Сервер является сервером реплики в данных в репликации.

    Дополнительные сведения о создании сервера, см. в разделе [Создание базы данных Azure для MariaDB сервера с помощью портала Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Необходимо создать базу данных Azure для MariaDB в ценовые категории общего назначения или оптимизированных для памяти.

2. Создайте учетные записи пользователей и соответствующие права доступа.
    
    Учетные записи пользователей, не реплицируются с главного сервера на сервер-реплику. Чтобы предоставить пользователю доступ к серверу-реплике, необходимо вручную создать все учетные записи и соответствующие привилегии на созданной базе данных Azure для MariaDB сервера.

## <a name="configure-the-master-server"></a>Настройка главного сервера

Следующие действия, Подготовка и настройка MariaDB размещенной на локальном сервере, на виртуальной Машине или в облачной службе базы данных для репликации данных в. MariaDB сервер является основным в данных в репликации.

1. Включите ведение двоичного журнала.
    
    Чтобы увидеть, если двоичные ведение журналов включается в образце, введите следующую команду:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Если переменная [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) возвращает значение `ON`, двоичные ведение журнала включено на сервере.

   Если `log_bin` возвращает значение `OFF`, изменить **my.cnf** файл, чтобы `log_bin=ON` включает ведение двоичного журнала. Перезапустите сервер, чтобы изменения вступили в силу.

2. Настройка параметров главного сервера.

    Репликация данных в требует указания параметра `lower_case_table_names` согласованность между сервером главный и реплика. `lower_case_table_names` Параметр имеет значение `1` по умолчанию в базе данных Azure для MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Создать новую роль репликации и настройка разрешений.

   Создание учетной записи пользователя на главном сервере, настроенную с правами репликации. Можно создать учетную запись с помощью команд SQL или MySQL Workbench. Если вы планируете выполнить репликацию с помощью протокола SSL, необходимо указать это при создании учетной записи пользователя.
   
   Чтобы узнать, как добавление учетных записей пользователей на главный сервер, см. в разделе [MariaDB документации](https://mariadb.com/kb/en/library/create-user/).

   С помощью следующих команд, новая роль репликации можно получить доступ к хозяину с любого компьютера, а не только компьютер, на котором размещена сам образец. Такой доступ, укажите **syncuser\@«%»** в команду, чтобы создать пользователя.
   
   Дополнительные сведения о документации MariaDB, см. в разделе [указав имена учетных записей](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Команда SQL**

   - Репликация с помощью SSL

       Чтобы требовать SSL для всех подключений пользователя, введите следующую команду, чтобы создать пользователя:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Репликации без SSL

       Если SSL не требуется для всех подключений, введите следующую команду, чтобы создать пользователя:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Чтобы создать эту роль репликации в MySQL Workbench в **управления** области выберите **пользователей и правами**. Затем выберите **Добавление учетной записи**.
 
   ![Пользователи и привилегии](./media/howto-data-in-replication/users_privileges.png)

   Введите имя пользователя в **имя входа** поля.

   ![Синхронизация пользователя](./media/howto-data-in-replication/syncuser.png)
 
   Выберите **административные роли** панели, а затем в списке **привилегии глобального**выберите **Replication Slave**. Выберите **применить** для создания роли репликации.

   ![Ведомая роль репликации](./media/howto-data-in-replication/replicationslave.png)


4. Задайте главный сервер в режим только для чтения.

   Прежде чем вы дамп базы данных, сервер должны находиться в режиме только для чтения. В режиме только для чтения, master не может обработать любые транзакции записи. Чтобы избежать влияния на бизнес, планируйте окне только для чтения время низкой нагрузки.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Получите текущее имя файла журнала в двоичном формате и смещение.

   Чтобы определить текущий двоичный файл журнала и смещение, выполните команду [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Результаты будут подобны таблице ниже:
   
   ![Результаты состояния основного сервера](./media/howto-data-in-replication/masterstatus.png)

   Обратите внимание на двоичный файл, так как он будет использоваться в последующих шагах.
   
6. Получает позицию GTID (необязательный, необходимые для репликации с помощью GTID).

   Запустите функцию [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) для получения позиции GTID соответствующее имя файла со значением binlog и смещение.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Дамп и восстановление главного сервера

1. Позволяет создавать копию всех баз данных с главного сервера.

   Используйте mysqldump для помещения в дамп все базы данных с главного сервера. Нет необходимости создать дамп библиотеки MySQL и тестовой библиотеки.

    Дополнительные сведения см. в разделе [дампа и восстановления](howto-migrate-dump-restore.md).

2. Установите режим чтения и записи на главном сервере.

   После записано базы данных, изменить образец MariaDB сервер обратно в режим чтения и записи.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Восстановите файл дампа на новом сервере.

   Восстановите файл дампа на сервере, созданном в базе данных Azure для MariaDB. См. в разделе [дампа и восстановить](howto-migrate-dump-restore.md) способ восстановить файл дампа на сервере MariaDB.

   Если большой файл дампа, передать его на виртуальную Машину в Azure в той же области, что сервер-реплика. Восстановите базу данных Azure для MariaDB из виртуальной Машины.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Свяжите серверы со главный и реплика должна запускаться репликация данных в

1. Задайте главного сервера.

   Все функции репликации входных данных выполняются хранимыми процедурами. Все процедуры можно найти в статье о [хранимых процедурах репликации входных данных](reference-data-in-stored-procedures.md). Хранимые процедуры могут выполняться в MySQL Workbench или оболочки MySQL.

   Чтобы связать два сервера и запуска репликации, войдите на целевой сервер реплики, в базе данных Azure для MariaDB службы. Затем установите внешних экземпляр в качестве главного сервера с помощью `mysql.az_replication_change_master` или `mysql.az_replication_change_master_with_gtid` хранимой процедуры для базы данных Azure для MariaDB сервера.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   или
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: имя узла главного сервера.
   - master_user: имя пользователя для главного сервера.
   - master_password: пароль для главного сервера.
   - master_log_file: имя файла двоичного журнала из выполняемой команды `show master status`.
   - master_log_pos: позиция в двоичном журнале из выполняемой команды `show master status`.
   - master_gtid_pos: Позиция GTID запуск `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: контекст сертификата ЦС. Если вы не используете SSL, передайте пустую string.*
    
    
    \* Рекомендуется передавать в параметре master_ssl_ca как переменную. Дополнительные сведения представлены в примерах ниже.

   **Примеры**

   - Репликация с помощью SSL

       Создайте переменную `@cert` , выполнив следующие команды:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Настройка репликации с помощью протокола SSL между главного сервера, размещенного в companya.com домена и сервера-реплики, размещенной в базе данных Azure для MariaDB. Эта хранимая процедура выполняется на реплике.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Репликации без SSL

       Настройка репликации без SSL между главного сервера, размещенного в companya.com домена и сервера-реплики, размещенной в базе данных Azure для MariaDB. Эта хранимая процедура выполняется на реплике.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Запуск репликации.

   Вызовите `mysql.az_replication_start` хранимая процедура должна запускаться репликация.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Проверьте состояние репликации.

   Вызовите команду [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) на сервере-реплике, чтобы просмотреть состояние репликации.
    
   ```sql
   show slave status;
   ```

   Если `Slave_IO_Running` и `Slave_SQL_Running` находятся в состоянии `yes`, а для параметра `Seconds_Behind_Master` является `0`, работает ли репликация. `Seconds_Behind_Master` указывает величину задержки на реплике. Если значение не `0`, а затем обрабатывает обновления реплики.

4. Обновите соответствующий переменных сервера, чтобы вносить в данных репликации более безопасные (требуется только для репликации без GTID).
    
    Из-за ограничений собственная репликация в MariaDB, необходимо задать [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) и [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) переменных на репликацию без GTID сценария.

    Проверьте подчиненного сервера `sync_master_info` и `sync_relay_log_info` переменные, чтобы убедиться, что репликация данных в стабильности и задайте для переменных `1`.
    
## <a name="other-stored-procedures"></a>Другие хранимые процедуры

### <a name="stop-replication"></a>Остановка репликации

Чтобы остановить репликацию между главным сервером и сервером-репликой, используйте следующую хранимую процедуру:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Удалить отношение репликации

Чтобы удалить связь между основной и реплика сервером, выполните следующую хранимую процедуру:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Пропустить ошибку репликации

Чтобы пропустить ошибки репликации и разрешить репликацию, используйте следующую хранимую процедуру:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Дальнейшие действия
См. дополнительные сведения о [репликации входных данных](concepts-data-in-replication.md) в базе данных Azure для MariaDB.
