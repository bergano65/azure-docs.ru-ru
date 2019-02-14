---
title: Настройка политик Apache HBase в HDInsight с Корпоративным пакетом безопасности — Azure
description: Сведения о настройке политик Apache Ranger для HBase в Azure HDInsight с Корпоративным пакетом безопасности.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: tutorial
ms.date: 02/01/2019
ms.openlocfilehash: e1a0dda4c13baf7fc2e5ba65d599db8c74591adb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893244"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Руководство по Настройка политик Apache HBase в HDInsight с Корпоративным пакетом безопасности (предварительная версия)

Сведения о настройке политик Apache Ranger для кластеров Apache HBase с Корпоративным пакетом безопасности (ESP). Кластеры ESP подключены к домену, благодаря чему пользователи могут проходить аутентификацию с учетными данными домена. В этом руководстве вы создадите две политики Ranger для ограничения доступа к различным семействам столбцов в таблице HBase.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание пользователей домена
> * Создание политик Ranger
> * Создание таблиц в кластере HBase.
> * Создание политик Ranger

## <a name="before-you-begin"></a>Перед началом работы

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).

* Войдите на [портале Azure](https://portal.azure.com/).

* Создайте [кластер HBase в HDInsight с Корпоративным пакетом безопасности](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Подключение к пользовательскому интерфейсу администратора Apache Ranger

1. В браузере подключитесь к интерфейсу администратора Ranger с помощью URL-адреса `https://<ClusterName>.azurehdinsight.net/Ranger/`. Не забудьте изменить `<ClusterName>` на имя вашего кластера HBase.

    > [!NOTE]  
    > Учетные данные Ranger не совпадают с учетными данными кластера Hadoop. Чтобы браузеры не использовали кэшированные учетные данные Hadoop, подключитесь к пользовательскому интерфейсу администратора Ranger в новом окне браузера в режиме InPrivate.

2. Зарегистрируйтесь, используя свои учетные данные администратора Azure Active Directory (AD). Учетные данные администратора Azure AD отличаются от учетных данных кластера HDInsight или учетных данных SSH узла Linux HDInsight.

## <a name="create-domain-users"></a>Создание пользователей домена

В статье [Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) можно узнать, как создать пользователей домена **sales_user1** и **marketing_user1**. В рабочем сценарии пользователи домена берутся из вашего клиента Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Создание таблиц HBase и импорт примера данных

Для подключения к кластерам HBase можно использовать протокол SSH, а для создания таблиц HBase, вставки данных и создания запросов к данным — [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html). Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Использование оболочки HBase

1. Из SSH выполните следующую команду HBase:
   
    ```bash
    hbase shell
    ```

2. Создайте таблицу HBase `Customers` с двумя семействами столбцов: `Name` и `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Вставьте какие-либо данные:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Просмотрите содержимое таблицы.
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![Оболочка HDInsight Hadoop HBase](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Создание политик Ranger

Создайте политику Ranger для пользователей **sales_user1** и **marketing_user1**.

1. Откройте **пользовательский интерфейс администратора Ranger**. Щелкните **\<Имя_кластера>_hbase** в разделе **HBase**.

   ![Пользовательский интерфейс администратора Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. На экране **List of Policies** (Список политик) отобразятся все политики Ranger, созданные для этого кластера. Может быть указана одна предварительно настроенная политика. Щелкните **Add New Policy** (Добавить новую политику).

    ![Политика создания пользовательского интерфейса администратора Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3.  На экране **Create Policy** (Создание политики) введите следующие значения.

   |**Параметр**  |**Рекомендуемое значение**  |
   |---------|---------|
   |Имя политики  |  sales_customers_name_contact   |
   |HBase Table (Таблица HBase)   |  Клиенты |
   |HBase Column-family (Семейство столбцов HBase)   |  Name, Contact |
   |HBase Column (Столбец HBase)   |  * |
   |Select Group (Выбрать группу)  | |
   |Выберите пользователя  | sales_user1 |
   |Разрешения  | чтение |

   Имя раздела может содержать следующие подстановочные знаки:

   * `*` обозначает ноль или более вхождений символов.
   * `?` означает один символ.

   ![Политика создания пользовательского интерфейса администратора Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Подождите несколько минут, пока Ranger синхронизируется с Azure AD, если в поле **Выберите пользователя** автоматически не подставится пользователь домена.

4. Щелкните **Добавить**, чтобы сохранить политику.

5. Щелкните **Добавить новую политику**, а затем введите следующие значения.

   |**Параметр**  |**Рекомендуемое значение**  |
   |---------|---------|
   |Имя политики  |  marketing_customers_contact   |
   |HBase Table (Таблица HBase)   |  Клиенты |
   |HBase Column-family (Семейство столбцов HBase)   |  Контакт |
   |HBase Column (Столбец HBase)   |  * |
   |Select Group (Выбрать группу)  | |
   |Выберите пользователя  | marketing_user1 |
   |Разрешения  | чтение |

   ![Политика создания пользовательского интерфейса администратора Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Щелкните **Добавить**, чтобы сохранить политику.

## <a name="test-the-ranger-policies"></a>Тестирование политик Ranger

В зависимости от настроенных политик Ranger, пользователь **sales_user1** может просматривать все данные столбцов в семействах столбцов `Name` и `Contact`. Пользователь **marketing_user1** может просматривать данные только в семействе столбцов `Contact`.

### <a name="access-data-as-salesuser1"></a>Получение доступа к данным как пользователь sales_user1

1. Откройте новое подключение SSH к кластеру. Чтобы войти в кластер, выполните следующую команду:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. С помощью команды kinit перейдите в контекст необходимого пользователя.

   ```bash
   kinit sales_user1
   ```

2. Откройте оболочку HBase и просмотрите таблицу `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Обратите внимание, что пользователь по продажам может просматривать все столбцы таблицы `Customers`, включая два столбца в семействе столбцов `Name`, а также пять столбцов в семействе столбцов `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Получение доступа к данным как пользователь marketing_user1

1. Откройте новое подключение SSH к кластеру. Выполните следующую команду, чтобы войти от имени пользователя **marketing_user1**.

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. С помощью команды kinit перейдите в контекст необходимого пользователя.

   ```bash
   kinit marketing_user1
   ```

2. Откройте оболочку HBase и просмотрите таблицу `Customers`.

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Обратите внимание, что торговый пользователь может просматривать только пять столбцов семейства столбцов `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Просмотрите события доступа к ресурсам аудита из интерфейса Ranger.

   ![Аудит политики пользовательского интерфейса Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер HBase, сделав следующее:

1. Войдите на [портале Azure](https://portal.azure.com/).
2. В поле **Поиск** в верхней части страницы введите **HDInsight**. 
1. Выберите **Кластеры HDInsight** в разделе **Службы**.
1. В списке кластеров HDInsight, который отобразится, щелкните **...** рядом с кластером, созданным при работе с этим руководством. 
1. Нажмите кнопку **Delete**(Удалить). Щелкните **Да**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Начало работы с Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
