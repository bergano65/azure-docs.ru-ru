---
title: Управление кластерами Корпоративный пакет безопасности в Azure HDInsight
description: Узнайте, как управлять кластерами Azure HDInsight с помощью Корпоративный пакет безопасности.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 35accc587fe197da751a8695e3ec0b21ea9fbbd4
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044919"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Управление кластерами HDInsight с помощью корпоративного пакета безопасности
Познакомьтесь с пользователями и ролями в корпоративном пакете безопасности (ESP) HDInsight, а также с тем, как управлять кластерами ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Использование Visual Studio Code для связывания присоединенного к домену кластера

Вы можете связать обычный кластер с помощью управляемого имени пользователя Apache Ambari, а кластер безопасности Apache Hadoop — с помощью имени пользователя домена (например `user1@contoso.com`).

1. Откройте палитру команд, нажав **CTRL+SHIFT+P** и введите **HDInsight: Link a cluster** (HDInsight: связать кластер).

   ![Палитра команд, связывание кластера](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Введите URL-адрес кластера HDInsight, имя пользователя и пароль и выберите тип кластера. Если проверка пройдена, отобразится сообщение об успешном выполнении.

   ![диалоговое окно "шаги процесса кластера ссылок"](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется имя пользователя и пароль для связывания.

3. Данные связанного кластера можно просмотреть с помощью команды **перечисления кластеров**. Теперь в этот связанный кластер можно отправить скрипт.

   ![Вывод списка проверки выходных данных команды кластера](./media/apache-domain-joined-manage/hdinsight-linked-cluster.png "связанный кластер")

4. Также можно удалить связь кластера. Для этого введите **HDInsight: Unlink a cluster** (HDInsight: удалить связь кластера) в палитре команд.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Использование IntelliJ для связывания присоединенного к домену кластера

Вы можете связать обычный кластер с помощью управляемого имени пользователя Ambari, а кластер безопасности — с помощью имени пользователя домена (например, `user1@contoso.com`).

1. Щелкните **Link a cluster** (Связывание кластера) в **обозревателе Azure**.

   ![IntelliJ контекстное меню кластера ссылок](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Введите **имя кластера**, **имя пользователя** и **пароль**. В случае сбоя аутентификации проверьте имя пользователя и пароль. При необходимости добавьте учетную запись хранения, ключ хранилища, затем выберите контейнер из контейнера хранилища. Информация о хранилище предназначена для обозревателя хранилищ, который находится в дереве слева

   ![Диалоговое окно кластера связи с обозревателем Azure IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется ключ к хранилищу данных, имя пользователя и пароль для связывания.
   > 
   > ![Учетная запись хранения Azure Explorer в IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

3. Если данные введены правильно, связанный кластер отобразится в узле **HDInsight**. Теперь в этот связанный кластер можно отправить приложение.

   ![IntelliJ связанного кластера Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png "IntelliJ связанного кластера]")

4. В **обозревателе Azure** также можно удалить связь кластера.

   ![Несвязанный кластер IntelliJ в Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Использование Eclipse для связывания присоединенного к домену кластера

Вы можете связать обычный кластер с помощью управляемого имени пользователя Ambari, а кластер безопасности — с помощью имени пользователя домена (например, `user1@contoso.com`).

1. Щелкните **Link a cluster** (Связывание кластера) в **обозревателе Azure**.

   ![Eclipse контекстное меню кластера ссылок](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Введите **имя кластера**, **имя пользователя** и **пароль**, затем нажмите кнопку "ОК", чтобы связать кластер. При необходимости введите учетную запись хранения, ключ к хранилищу данных, а затем выберите в представлении в виде дерева контейнер хранилища для обозревателя хранилищ.

   ![Диалоговое окно кластера ссылок Azure Explorer Eclipse](./media/apache-domain-joined-manage/link-cluster-dialog1.png)

   > [!NOTE]  
   > Если кластер зарегистрирован в подписке Azure и связан, используется ключ к хранилищу данных, имя пользователя и пароль для связывания.
   > 
   > ![Учетная запись хранения Azure Explorer в Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Если данные введены правильно, связанный кластер отобразится в узле **HDInsight** после нажатия кнопки "ОК". Теперь в этот связанный кластер можно отправить приложение.

   ![Eclipse для связанного кластера в Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. В **обозревателе Azure** также можно удалить связь кластера.
   
   ![Несвязанная Eclipse кластера Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Получите доступ к кластерам с помощью пакета безопасности предприятия.

Пакет безопасности предприятия (прежнее название — HDInsight Premium) обеспечивает многопользовательский доступ к кластеру, где выполняется аутентификация Active Directory, а также авторизация с использованием Apache Ranger и ACL ADLS. Авторизация обеспечивает безопасное разделение пользователей, разрешая только привилегированным лицам доступ к данным на основе политик авторизации.

Обеспечение безопасности и изоляция пользователей важны для кластера HDInsight с пакетом безопасности предприятия. Для выполнения этих требований SSH-доступ к кластеру с пакетом безопасности предприятия блокируется. В следующей таблице описаны способы доступа, рекомендуемые для каждого типа кластера:

|Рабочая нагрузка|Сценарий|Способ доступа к данным|
|--------|--------|-------------|
|Apache Hadoop|Hive — интерактивные задания и запросы  |<ul><li>[Beeline](#beeline)</li><li>[Представление Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Инструменты Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Интерактивные задания и запросы, интерактивные задания PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin с Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Представление Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Инструменты Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Сценарии пакетной службы — отправка Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Интерактивный запрос (LLAP)|Интерактивный режим|<ul><li>[Beeline](#beeline)</li><li>[Представление Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Инструменты Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Любой|Установка пользовательского приложения|<ul><li>[Действия скрипта](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > В Корпоративном пакете безопасности записная книжка Jupyter не установлена или не поддерживается.

Использование стандартных API помогает обеспечить безопасность. Кроме того, вы получаете такие преимущества:

- **Управление** — вы можете управлять кодом и заданиями автоматизации с помощью стандартных API — Livy, HS2 и т. д.
- **Аудит** — используя SSH, вы не можете выполнять проверять, какие пользователи получили SSH-доступ к кластеру. Этого можно избежать, создавая задания через стандартные конечные точки, так как они будут выполняться в контексте пользователя. 



### <a name="beeline"></a>Использование Beeline 
Установите Beeline на компьютер подключитесь через общедоступный сегмент Интернета, используя следующие параметры: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Если Beeline установлен локально и подключение устанавливается через виртуальную сеть Azure, используйте следующие параметры: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Чтобы найти полное доменное имя головного узла, используйте сведения из руководства по получению полного доменного имени для узлов кластера.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Пользователи кластеров HDInsight с корпоративным пакетом безопасности
При создании кластера HDInsight без ESP в нем создаются две учетные записи пользователя:

* **Администратор Ambari**: эта учетная запись называется также *Пользователь Hadoop* или *Пользователь HTTP*. Эту учетную запись можно использовать для входа в Ambari по адресу https://&lt;имя_кластера >. azurehdinsight. NET. Его также можно использовать для выполнения запросов к представлениям Ambari, выполнения заданий через внешние средства (например, PowerShell, Templeton, Visual Studio) и проверки подлинности с помощью драйвера Hive ODBC и средств бизнес-аналитики (например, Excel, Power BI или Tableau).

В кластере HDInsight с корпоративным пакетом безопасности, кроме администратора Ambari, создаются еще три пользователя.

* **Администратор Ranger**: это локальная учетная запись администратора Apache Ranger. Она не является пользователем домена Active Directory. Эту учетную запись можно использовать для настройки политик, создания других пользователей-администраторов или делегированных администраторов (чтобы они могли управлять политиками). По умолчанию используется имя пользователя *admin* и такой же пароль, как для администратора Ambari. Этот пароль можно изменить на странице настроек в Ranger.
* **Пользователь домена и администратор кластера**: это пользователь домена Active Directory, который является администратором кластера Hadoop, а также служб Ambari и Ranger. Учетные данные этого пользователя нужно предоставить во время создания кластера. Этот пользователь имеет следующие права.

  * Присоединение компьютеров к домену и помещение их в определенное подразделение, которое указывается во время создания кластера.
  * Создание субъектов-служб в определенном подразделении, которое указывается во время создания кластера.
  * Создание обратных записей DNS.

    Обратите внимание, что эти права есть и у других пользователей AD.

    В пределах кластера существуют некоторые конечные точки (например, Templeton), которые не управляются службой Ranger, и следовательно не являются безопасными. Такие конечные точки закрываются для всех пользователей, за исключением пользователя домена и администратора кластера.
* **Обычный**: во время создания кластера можно указать несколько групп Active Directory. Пользователи в эти группах будут синхронизированы с Ranger и Ambari. Эти пользователи являются пользователями домена и будут иметь доступ только к конечным точкам, которые управляются службой Ranger (например, Hiveserver2). Для этих пользователей применяются все политики RBAC и правила аудита.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Роли кластеров HDInsight с корпоративным пакетом безопасности
Корпоративный пакет безопасности HDInsight предоставляет следующие роли:

* администратор кластера;
* оператор кластера;
* Администратор служб
* оператор службы;
* пользователь кластера.

**Просмотр разрешений для этих ролей**

1. Откройте пользовательский интерфейс управления Ambari.  См. раздел [Вход в пользовательский интерфейс управления Ambari](#open-the-ambari-management-ui).
2. В меню слева щелкните **Роли**.
3. Щелкните синий вопросительный знак, чтобы просмотреть разрешения.

    ![Разрешения роли HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Вход в пользовательский интерфейс управления Ambari

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight.
3. В меню сверху щелкните **Панель мониторинга**, чтобы открыть Ambari.
4. Войдите в Ambari с помощью имени пользователя домена администратора кластера и пароля.
5. Щелкните раскрывающееся меню **Администратор** в правом верхнем углу, затем нажмите кнопку **Управление Ambari**.

    ![ESP HDInsight управление Ambari Apache](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Пользовательский интерфейс выглядит примерно так:

    ![Пользовательский интерфейс управления ESP HDInsight Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Список пользователей домена, синхронизированных из Active Directory
1. Откройте пользовательский интерфейс управления Ambari.  См. раздел [Вход в пользовательский интерфейс управления Ambari](#open-the-ambari-management-ui).
2. В меню слева щелкните **Users**(Пользователи). Вы увидите всех пользователей, синхронизированных в кластер HDInsight из Active Directory.

    ![Список пользователей интерфейса управления Ambari для HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Список групп домена, синхронизированных из Active Directory
1. Откройте пользовательский интерфейс управления Ambari.  См. раздел [Вход в пользовательский интерфейс управления Ambari](#open-the-ambari-management-ui).
2. В меню слева щелкните **Группы**. Вы увидите все группы, синхронизированные в кластер HDInsight из Active Directory.

    ![Список групп интерфейса управления Ambari для HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Настройка разрешений для представлений Hive
1. Откройте пользовательский интерфейс управления Ambari.  См. раздел [Вход в пользовательский интерфейс управления Ambari](#open-the-ambari-management-ui).
2. В меню слева щелкните **Представления**.
3. Щелкните **HIVE**, чтобы открыть подробную информацию.

    ![Представления Hive в интерфейсе управления Ambari для HDInsight с корпоративным пакетом безопасности](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Щелкните ссылку **Представление Hive**, чтобы настроить представления Hive.
5. Выполните прокрутку вниз до раздела **Разрешения**.

    ![Настройка разрешений для представлений Hive в интерфейсе управления Ambari для HDInsight с ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Щелкните **Добавить пользователя** или **Добавить группу**, а затем укажите пользователей или группы, которые могут использовать представления Hive.

## <a name="configure-users-for-the-roles"></a>Настройка ролей для пользователей
 Список ролей и соответствующих разрешений см. в разделе "Роли кластеров HDInsight с корпоративным пакетом безопасности".

1. Откройте пользовательский интерфейс управления Ambari.  См. раздел [Вход в пользовательский интерфейс управления Ambari](#open-the-ambari-management-ui).
2. В меню слева щелкните **Роли**.
3. Щелкните **Добавить пользователя** или **Добавить группу**, чтобы назначить роли для пользователей и групп.

## <a name="next-steps"></a>Дальнейшие действия
* Описание настройки кластера HDInsight с корпоративным пакетом безопасности см. в разделе [Настройка кластеров HDInsight с корпоративным пакетом безопасности](apache-domain-joined-configure.md).
* Описание настройки политик Hive и выполнения запросов Hive см. в статье [Настройка политик Apache Hive в HDInsight с Корпоративным пакетом безопасности](apache-domain-joined-run-hive.md).
