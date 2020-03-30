---
title: Настройка репликации данных - База данных Azure для MariaDB
description: В этой статье описывается, как настроить репликацию данных в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530161"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Настройка репликации данных в базу данных Azure для MariaDB

В этой статье описывается, как настроить репликацию data-in в базе данных Azure для MariaDB путем настройки серверов master и реплики. Эта статья предполагает, что у вас есть некоторый предыдущий опыт работы с серверами и базами данных MariaDB.

Для создания реплики в базе данных Azure для службы MariaDB репликация Data-in синхронизирует данные с сервера Master MariaDB на базе данных, в виртуальных машинах (VMs) или в службах облачных баз данных.

> [!NOTE]
> Если ваш основной сервер является версией 10.2 или более новой, мы рекомендуем настроить репликацию Data-in с помощью [Идентификации глобальных транзакций.](https://mariadb.com/kb/en/library/gtid/)


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Создание сервера MariaDB для использования в качестве реплики

1. Создайте новую базу данных Azure для сервера MariaDB (например, replica.mariadb.database.azure.com). Сервер является сервером реплики в репликации Data-in.

    Чтобы узнать о создании сервера, [см. Создать базу данных Azure для сервера MariaDB с помощью портала Azure.](quickstart-create-mariadb-server-database-using-azure-portal.md)

   > [!IMPORTANT]
   > Необходимо создать базу данных Azure для сервера MariaDB на уровне оценок общего назначения или памяти.

2. Создание идентичных учетных записей пользователей и соответствующих привилегий.
    
    Учетные записи пользователей не реплицируются с основного сервера на сервер реплики. Чтобы обеспечить пользователю доступ к серверу реплик, необходимо вручную создать все учетные записи и соответствующие привилегии на недавно созданной базе данных Azure для сервера MariaDB.

## <a name="configure-the-master-server"></a>Настройка главного сервера

Следующие шаги готовят и настраивали сервер MariaDB, размещенный на территории, в VM или в облачной базе данных для репликации Data-in. Сервер MariaDB является мастером репликации data-in.

1. Включите двоичную регистрацию.
    
    Чтобы увидеть, включена ли двоичная регистрация в оригинале, введите следующую команду:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Если переменная [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) возвращает `ON`значение, на вашем сервере включена двоичная регистрация.

   Если `log_bin` возвращает `OFF`значение, отодевать файл `log_bin=ON` **my.cnf** так, что включает сядвочные журналы. Перезапустите сервер, чтобы изменение вступило в силу.

2. Настройка настройки сервера.

    Репликация data-in требует, чтобы параметр `lower_case_table_names` был согласован между серверами master и replica. Параметр `lower_case_table_names` установлен `1` по умолчанию в базе данных Azure для MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Создайте новую роль репликации и наверсните разрешения.

   Создайте учетную запись пользователя на главном сервере, настроенную с привилегиями репликации. Вы можете создать учетную запись, используя команды S'L или MyS'L Workbench. Если вы планируете воспроизвести с Помощью SSL, вы должны указать это при создании учетной записи пользователя.
   
   Чтобы узнать, как добавить учетные записи пользователей на вашем главном сервере, смотрите [документацию MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Используя следующие команды, новая роль репликации может получить доступ к мастеру из любой машины, а не только машины, вмещающих самого мастера. Для этого доступа укажите **синхронизатор\@'%'** в команде для создания пользователя.
   
   Чтобы узнать больше о [документации](https://mariadb.com/kb/en/library/create-user/#account-names)MariaDB, см.

   **Команда SQL**

   - Репликация с использованием SSL

       Чтобы потребовать SSL для всех подключений пользователей, введите следующую команду для создания пользователя:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Репликация без SSL

       Если SSL не требуется для всех соединений, введите следующую команду для создания пользователя:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Для создания роли репликации в MyS'L Workbench, в панели **управления,** выберите **пользователей и привилегий.** Затем выберите **Добавить учетную запись**.
 
   ![Пользователи и привилегии](./media/howto-data-in-replication/users_privileges.png)

   Введите имя пользователя в поле **имени входа.**

   ![Синхронизация пользователя](./media/howto-data-in-replication/syncuser.png)
 
   Выберите панель **административных ролей,** а затем в списке **глобальных привилегий**, выберите **Раб репликации.** Выберите **Применить** для создания роли репликации.

   ![Ведомая роль репликации](./media/howto-data-in-replication/replicationslave.png)


4. Установите мастер-сервер в режим чтения только.

   Перед тем, как сбросить базу данных, сервер должен быть помещен в режим только для чтения. Находясь в режиме только для чтения, мастер не может обрабатывать транзакции записи. Чтобы избежать влияния на бизнес, запланируйте окно только для чтения во время непикового времени.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Получите текущее имя файла двоичного журнала и смещение.

   Чтобы определить текущее имя файла двоичного журнала и смещение, запустите команду. [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)
    
   ```sql
   show master status;
   ```
   Результаты должны быть аналогичны следующей таблице:
   
   ![Результаты состояния основного сервера](./media/howto-data-in-replication/masterstatus.png)

   Обратите внимание на имя двоичного файла, потому что оно будет использоваться в последующих шагах.
   
6. Получите позицию GTID (необязательно, необходимую для репликации с помощью GTID).

   Запустите [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) функцию, чтобы получить позицию GTID для соответствующего имени файла бинлога и смещения.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Сброс и восстановление основного сервера

1. Сбросьте все базы данных с генерального сервера.

   Используйте mysqldump, чтобы сбросить все базы данных с генерального сервера. Не обязательно сбрасывать библиотеку myS'L и тестовую библиотеку.

    Для получения дополнительной информации [см.](howto-migrate-dump-restore.md)

2. Установите режим работы с начальн-сервером для чтения/записи.

   После того, как база данных была сброшена, измените сервер MariaDB обратно в режим чтения/записи.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Восстановление файла дампа на новый сервер.

   Восстановите файл дампа на сервере, созданном в базе данных Azure для MariaDB. О том, как восстановить файл дампа на сервере MariaDB, читайте в материале [«& Восстановление».](howto-migrate-dump-restore.md)

   Если файл дампа большой, загрузите его в VM в Azure в том же регионе, что и ваш сервер реплики. Восстановить его в базу данных Azure для сервера MariaDB с VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Связать серверы master и реплики для запуска репликации данных

1. Установите мастер-сервер.

   Все функции репликации входных данных выполняются хранимыми процедурами. Все процедуры можно найти в статье о [хранимых процедурах репликации входных данных](reference-data-in-stored-procedures.md). Сохраненные процедуры могут быть запущены в оболочке MyS'L или MyS'L Workbench.

   Чтобы связать два сервера и начать репликацию, восвай на целевой сервер реплики в Azure DB для службы MariaDB. Затем установите внешний экземпляр в качестве `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` главного сервера, используя или хранящуюся процедуру на Azure DB для сервера MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   или диспетчер конфигурации служб
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: имя узла главного сервера.
   - master_user: имя пользователя для главного сервера.
   - master_password: пароль для главного сервера.
   - master_log_file: имя файла двоичного журнала из выполняемой команды `show master status`.
   - master_log_pos: позиция в двоичном журнале из выполняемой команды `show master status`.
   - master_gtid_pos: позиция GTID от работы`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: контекст сертификата CA. Если вы не используете SSL, передайте в пустую строку.
    
    
    «Рекомендуем передавать в master_ssl_ca параметр в качестве переменной. Дополнительные сведения представлены в примерах ниже.

   **Примеры**

   - Репликация с использованием SSL

       Создайте `@cert` переменную, запустив следующие команды:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Репликация с помощью SSL настраивается между мастер-сервером, размещенным в домене companya.com, и сервером реплики, размещенным в базе данных Azure для MariaDB. Эта хранимая процедура выполняется на реплике.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Репликация без SSL

       Репликация без SSL настраивается между мастер-сервером, размещенным в домене companya.com, и сервером реплики, размещенным в базе данных Azure для MariaDB. Эта хранимая процедура выполняется на реплике.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Начало репликации.

   Вызовите сохраненную `mysql.az_replication_start` процедуру, чтобы начать репликацию.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Проверьте состояние репликации.

   Вызов [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) команды на сервер реплики для просмотра состояния репликации.
    
   ```sql
   show slave status;
   ```

   Если `Slave_IO_Running` `Slave_SQL_Running` и находятся `yes`в состоянии, и `Seconds_Behind_Master` значение, `0`репликация работает. `Seconds_Behind_Master` указывает величину задержки на реплике. Если значение не `0`является, то реплика обрабатывает обновления.

4. Обновление соответствующих переменных сервера, чтобы сделать репликацию данных более безопасной (требуется только для репликации без GTID).
    
    Из-за ограничения репликации в MariaDB [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) необходимо [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) установить и переменные на репликации без сценария GTID.

    Проверьте сервер и `sync_master_info` `sync_relay_log_info` переменные рабского сервера, чтобы убедиться, что репликация `1`данных стабильна, и установите переменные.
    
## <a name="other-stored-procedures"></a>Другие хранимые процедуры

### <a name="stop-replication"></a>Остановка репликации

Чтобы остановить репликацию между главным сервером и сервером-репликой, используйте следующую хранимую процедуру:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Удалить отношение репликации

Чтобы удалить связь между сервером-рекопией, используйте следующую сохраненную процедуру:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Пропустить ошибку репликации

Чтобы пропустить ошибку репликации и разрешить репликацию, используйте следующую сохраненную процедуру:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Дальнейшие действия
См. дополнительные сведения о [репликации входных данных](concepts-data-in-replication.md) в базе данных Azure для MariaDB.
