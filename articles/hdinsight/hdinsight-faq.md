---
title: Azure HDInsight часто задавал вопросы
description: Часто задаваемые вопросы о HDInsight
keywords: часто задаваемые вопросы, faq
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652219"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Часто задаваемые вопросы

В этой статье содержатся ответы на некоторые из наиболее распространенных вопросов о том, как запустить [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Создание или удаление кластеров HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Как обеспечить кластер HDInsight?

Для анализа типов доступных кластеров HDInsight и методов подготовки см. Настройка [кластеров в HDInsight с Apache Hadoop, Apache Spark, Apache Kafka и другими.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Как удалить существующий кластер HDInsight?

Чтобы узнать больше об удалении кластера, когда он больше не используется, [см.](hdinsight-delete-cluster.md)

Оставьте не менее 30-60 минут между операциями создания и удаления. В противном случае операция может выполнить неудачу со следующим сообщением об ошибке:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Как выбрать правильное количество ядер или узлов для рабочей нагрузки?

Соответствующее количество ядер и другие параметры конфигурации зависят от различных факторов.

Для получения дополнительной [Capacity planning for HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)информации см.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Что можно сделать при сбое подготовки кластера из-за проблемы с емкостью?

Общие ошибки проблемы емкости и методы смягчения данных приведены в этом разделе.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Ошибка: развертывание превысит квоту '800'

В Azure предоставляется квота на 800 развертываний на группу ресурсов. Различные квоты применяются в группе ресурсов, подписке, учетной записи или других областях. Например, для подписки может быть настроено ограничение числа ядер для региона. Если вы попытаетесь развернуть виртуальную машину с большим количеством ядер, чем разрешенная сумма, вы получите сообщение об ошибке, в которое говорится, что квота была превышена.

Чтобы решить эту проблему, удалите развертывания, которые больше не нужны, используя портал Azure, CLI или PowerShell.

Дополнительные сведения см. в статье [устранении ошибок, связанных с квотами ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Ошибка: Максимальный узлы превысили доступные ядра в этом регионе

Для подписки может быть настроено ограничение числа ядер для региона. Если вы попытаетесь развернуть ресурс, в который имеется больше ядер, чем разрешенная сумма, вы получите сообщение об ошибке, в которое говорится, что квота была превышена.

Чтобы запросить увеличение квоты, сделайте следующее:

1. Перейдите на [портал Azure](https://portal.azure.com)и выберите **поддержку Справки .**
   
1. Выберите **новый запрос на поддержку**.
   
1. На вкладке **"Основы"** на странице **запроса новой поддержки** укажите следующую информацию:
   
   - **Тип выпуска:** Выберите **ограничения службы и подписки (квоты)**.
   - **Подписка:** Выберите подписку, изменяемую.
   - **Тип квоты:** Выберите **HDInsight**.

См. сведения о [создании запроса в службу поддержки для увеличения количества ядер](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Каковы различные типы узлов в кластере HDInsight?

Кластеры Azure HDInsight имеют различные типы виртуальных машин или узлов. Каждый тип узла играет определенную роль в работе системы.

Для получения дополнительной информации смотрите [типы ресурсов в кластерах Azure HDInsight.](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)

## <a name="individual-components"></a>Отдельные компоненты

### <a name="can-i-install-additional-components-on-my-cluster"></a>Могу ли я установить дополнительные компоненты в свой кластер?

Да. Для установки дополнительных компонентов или настройки конфигурации кластера используйте:

- Сценарии во время или после создания. Скрипты вызываются с помощью [действия скрипта,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)который представляет собой опцию конфигурации, которую можно использовать с портала Azure, hdInsight Windows PowerShell cmdlets или HDInsight .NET SDK. Эта опция конфигурации может быть использована с портала Azure, HDInsight Windows PowerShell cmdlets или HDInsight .NET SDK.

- [Платформа приложений HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) для установки экосистемных приложений.

Список поддерживаемых компонентов можно найти в [компонентах и версиях Apache Hadoop с HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Могу ли я обновить отдельные компоненты, которые предварительно установлены на кластере?

При обновлении встроенных компонентов или приложений, которые предварительно установлены в кластере, полученная конфигурация не будет поддерживаться корпорацией Майкрософт. Эти системные конфигурации не были протестированы корпорацией Майкрософт. Попробуйте использовать другую версию кластера HDInsight, которая, возможно, уже имеет обновленную версию компонента предустановленной.

Например, обновление Hive как отдельного компонента не поддерживается. HDInsight является управляемой службой, и многие службы интегрированы с сервером Ambari и протестированы. Модернизация Hive сама по себе приводит к изменению индексированных связных файлов других компонентов и вызовет проблемы интеграции компонентов в кластере.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Могут ли Spark и Kafka работать в одном кластере HDInsight?

Нет, невозможно запустить Apache Kafka и Apache Spark в одном и том же кластере HDInsight. Создание отдельных кластеров для Kafka и Spark, чтобы избежать проблем с ресурсами.

### <a name="how-do-i-change-timezone-in-ambari"></a>Как изменить часовой пояс в Амбари?

1. Откройте веб-uI Ambari в `https://CLUSTERNAME.azurehdinsight.net`, где CLUSTERNAME это имя вашего кластера.
2. В правом верхнем углу выберите админ Параметры. 

   ![Настройки Амбари](media/hdinsight-faq/ambari-settings.png)

3. В окне настроек пользователя выберите новый часовой пояс из временного пояса, а затем нажмите Сохранить.

   ![Настройки пользователя Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Хранилище мета-данных

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Как перейти из существующего метамагазина в Azure S'L Server? 

Для перехода с сервера S'L на Azure S'L Server [см.](../dms/tutorial-sql-server-to-azure-sql.md)

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Удаляется ли метамагазин Hive при удалении кластера?

Это зависит от типа метамагазина, который настроен для использования кластера.

Для метамагазина по умолчанию: метамагазин по умолчанию является частью жизненного цикла кластера. При удалении кластера соответствующее хранилище метаданных и метаданные также удаляются.

Для пользовательского метамагазина: жизненный цикл метамагазина не привязан к жизненному циклу кластера. Таким образом, можно создавать и удалять кластеры без потери метаданных. Метаданные, такие как схемы Hive, сохраняются даже после удаления и воссоздания кластера HDInsight.

Дополнительную информацию см. в статье [Использование внешних хранилищ метаданных в Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Мигрирует ли метамагазин Hive также миграцию политик по умолчанию в базу данных Ranger?

Нет, определение политики находится в базе данных Ranger, поэтому миграция базы данных Ranger будет мигрировать в свою политику.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Можно ли перенести метамагазин Hive из кластера Enterprise Security Package (ESP) в кластер, не связанный с ESP, и наоборот?

Да, можно перенести метамагазин Hive из ESP в кластер, не связанный с ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Как оценить размер базы данных metastore Hive?

Метамагазин Hive используется для хранения метаданных для источников данных, которые используются сервером Hive. Требования к размеру частично зависят от количества и сложности ваших источников данных Hive и не могут быть оценены заранее. Как указано в [руководстве Hive metastore,](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)вы можете начать с уровня S2, который обеспечивает 50 DTU и 250 ГБ памяти, и если вы видите узкое место, вы можете масштабировать базу данных.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Поддерживаете ли вы какую-либо другую базу данных, кроме базы данных Azure S'L, как внешний метамагазин?

Нет, корпорация Майкрософт поддерживает только базу данных Azure S'L в качестве внешнего пользовательского метамагазина.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Могу ли я поделиться метамагазином в нескольких кластерах?

Да, вы можете обмениваться пользовательским метамагазином в нескольких кластерах, если они используют одну и ту же версию HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Связь и виртуальные сети  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Каковы последствия блокировки портов 22 и 23 в моей сети?

Если вы блокируете порты 22 и порт 23, у вас не будет доступа SSH к кластеру. Эти порты не используются службой HDInsight.

Дополнительные сведения см. в следующих документах:

- [Контроль сетевого трафика](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Безопасный входящий трафик кластеров HDInsight в виртуальной сети с частной конечной точкой](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-адреса управления HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Могу ли я развернуть дополнительную виртуальную машину в той же подсети, что и кластер HDInsight?

Да, вы можете развернуть дополнительную виртуальную машину в той же подсети, что и кластер HDInsight. Возможны следующие конфигурации:

- Края: К кластеру можно добавить еще один узлы края, как описано в [пустых узлах Epache Hadoop в HDInsight.](hdinsight-apps-use-edge-node.md)

- Автономные узлы: Вы можете добавить автономную виртуальную машину в ту же подсеть `https://<CLUSTERNAME>-int.azurehdinsight.net`и получить доступ к кластеру из этой виртуальной машины с помощью частной точки. Для получения дополнительной информации смотрите [Контроль сетевого трафика](hdinsight-plan-virtual-network-deployment.md#networktraffic).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Должен ли я хранить данные на локальном диске кромки?

Нет, хранение данных на локальном диске не является хорошей идеей. Если узла выведет из строя, все данные, хранящиеся локально, будут потеряны. Мы рекомендуем хранить данные в хранилище Azure Data Lake Data Data Data Gen2 или Хранилище Azure Blob или путем установки совместного использования файлов Azure для хранения данных.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Могу ли я добавить существующий кластер HDInsight в другую виртуальную сеть?

Нет, Вы не можете. Виртуальная сеть должна быть указана во время подготовки. Если во время подготовки не указана виртуальная сеть, развертывание создает внутреннюю сеть, которая недоступна извне. Для получения дополнительной информации [см.](hdinsight-plan-virtual-network-deployment.md#existingvnet)

## <a name="security-and-certificates"></a>Безопасность и сертификаты

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Каковы рекомендации по защите вредоносных программ в кластерах Azure HDInsight?

Для получения информации [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../security/fundamentals/antimalware.md)о защите от вредоносных программ см.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Как создать ключ для кластера HDInsight ESP?

Создайте ключ Kerberos для вашего имени пользователя домена. Позже этот ключ можно использовать для проверки подлинности удаленных кластеров, связанных с доменом, без ввода пароля. Доменное имя верхнее:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Могу ли я использовать существующий арендатор Active Directory Azure для создания кластера HDInsight с ESP?

Прежде чем создать кластер HDInsight с ESP, необходимо включить службы активных доменов Azure Active Directory Domains (Azure AD DS). С открытым исходным кодом Hadoop полагается на Kerberos для аутентификации (в отличие от OAuth).

Чтобы присоединиться к VMs в домене, вы должны иметь контроллер домена. Azure AD DS является управляемым контроллером домена и считается расширением Active Directory Azure, который предоставляет все требования Kerberos для создания безопасного кластера Hadoop управляемым способом. HDInsight как управляемая служба интегрируется с Azure AD DS для обеспечения сквозной безопасности.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Могу ли я использовать сертификат, подписанный самостоятельно, в безопасной установке LDAP AAD-DS и предоставить кластер ESP?

Рекомендуется использовать сертификат, выданный органом по сертификации, но использование сертификата, подписанного самостоятельно, также поддерживается на ESP. Дополнительные сведения можно найти в разделе

- [Включение доменных служб Azure Active Directory](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Учебник: Настройка безопасного LDAP для домена идочебных служб Active Directory](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Как я могу вытащить активность входа, показанную в Ranger?

Для требований аудита корпорация Майкрософт рекомендует включить журналы Azure Monitor, описанные в [журналах Use Ure Monitor, для мониторинга кластеров HDInsight.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Могу ли я отключить Clamscan в моем кластере?

Clamscan — это антивирусное программное обеспечение, которое работает в кластере HDInsight и используется системой безопасности Azure (azsecd) для защиты кластеров от вирусных атак. Корпорация Майкрософт настоятельно рекомендует пользователям воздерживаться от внесения каких-либо изменений в конфигурацию Clamscan по умолчанию.

Этот процесс не вмешивается и не отнимает циклов от других процессов. Он всегда уступит другому процессу. Пики процессора из Clamscan следует увидеть только тогда, когда система простаивает.  

В сценариях, в которых необходимо контролировать расписание, можно использовать следующие действия:

1. Отключить автоматическое выполнение с помощью следующей команды:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Добавьте задание Cron, выполняемый следующей командой в качестве корня:
   
   `/usr/local/bin/azsecd manual -s clamav`

Для получения дополнительной информации о том, как настроить и запустить работу cron, [см. Как я могу настроить работу Cron?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Почему LLAP доступен в кластерах Spark ESP?
В кластерах ESP Spark LLAP включен по соображениям безопасности (например, Apache Ranger), а не по производительности. Следует использовать более крупные vMs-назаковые центры для размещения ресурсов LLAP (например, минимальный D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Как добавить дополнительные группы AAD после создания кластера ESP?
Есть два способа достижения этой цели: 1- Вы можете воссоздать кластер и добавить дополнительную группу во время создания кластера. Если вы используете синхронизацию в AAD-DS, пожалуйста, убедитесь, что группа B включена в синхронизацию.
2- Добавьте группу в качестве вложенной подгруппы предыдущей группы, которая использовалась для создания кластера ESP. Например, если вы создали кластер ESP с `A`группой, `B` вы можете позже `A` добавить группу в качестве вложенной подгруппы и примерно через час он будет синхронизирован и доступен в кластере автоматически. 

## <a name="storage"></a>Хранилище

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Могу ли я добавить данный элемент Azure Data Lake Storage 2 в существующий кластер HDInsight в качестве дополнительной учетной записи для хранения данных?

Нет, в настоящее время невозможно добавить учетную запись хранения данных Azure Data Lake Storage Gen2 в кластер, который имеет хранилище капли в качестве основного хранилища. Для получения дополнительной информации [см.](hdinsight-hadoop-compare-storage-options.md)

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Как найти в настоящее время связанную службу директора по хранению данных?

Настройки можно найти в **доступе Data Lake Storage Gen1 под** свойствами кластера на портале Azure. Для получения дополнительной информации [см.](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Как рассчитать использование учетных записей хранения данных и контейнеров с каплями для кластеров HDInsight?

Выполните одно из следующих действий.

- [Использование PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Найдите размер */пользователя/улей/. Корзина /* папка на кластере HDInsight, используя следующую командную строку:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Как настроить аудит для моей учетной записи хранения капли?

Для аудита учетных записей хранения blob назначайте мониторинг с помощью процедуры на [портале Monitor на портале Azure.](../storage/common/storage-monitor-storage-account.md) В журнале HDFS-аудит содержится только информация о аудите только для локальной файловой системы HDFS (hdfs://mycluster).  Он не включает операции, которые выполняются на удаленном хранении.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Как я могу передавать файлы между контейнером с каплей и головным узлам HDInsight?

Выполнить сценарий, похожий на следующий сценарий оболочки на головном уде:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Файлы *filenames.txt* будут иметь абсолютный путь файлов в контейнерах капли.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Есть ли плагины Ranger для хранения?

В настоящее время не существует плагина Ranger для хранения каблов и хранения azure Data Lake Storage Gen1 или Gen2. Для кластеров ESP следует использовать хранилище озер Данных Azure, поскольку можно, по крайней мере, устанавливать мелкозернистые разрешения вручную на уровне файловой системы с помощью инструментов HDFS. Кроме того, при использовании Azure Data Lake Storage кластеры ESP будут выполнять часть управления доступом файловой системы с помощью Active Directory Azure на уровне кластеров. 

Политики доступа к данным можно назначить группам безопасности пользователей с помощью Azure Storage Explorer. Дополнительные сведения можно найти в разделе

- [Как установить разрешения для пользователей Azure AD на запрос данных в Data Lake Storage Gen2 с помощью Hive или других служб?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Установка разрешений на уровне файлов и каталогов в Azure Data Lake Storage 2-го поколения с помощью Обозревателя службы хранилища Azure](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Могу ли я увеличить хранение HDFS в кластере без увеличения размера диска рабочих узлов?

Нет, вы не можете увеличить размер диска любого рабочего узла, поэтому единственный способ увеличить размер диска — это упасть с кластера и воссоздать его с помощью больших универсальных VMs. Не используйте HDFS для хранения любых данных HDInsight, поскольку данные удаляются при удалении кластера. Вместо этого храните данные в Azure. Масштабирование кластера также может добавить дополнительную емкость в кластер HDInsight.

## <a name="edge-nodes"></a>Граничные узлы

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Могу ли я добавить узлы края после создания кластера?

Кластер HDInsight или новый кластер при создании кластера. Дополнительные сведения см. в разделе [Использование пустых граничных узлов в кластерах Apache Hadoop в HDInsight](hdinsight-apps-use-edge-node.md)

### <a name="how-can-i-connect-to-an-edge-node"></a>Как подключиться к краю узла?

После создания кромки узла можно подключиться к нему с помощью SSH в порту 22. На портале кластера можно найти название узла края. Имена обычно заканчиваются *- ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Почему сохраняющиеся скрипты не работают автоматически на вновь созданных узлах края?

Сохраняются скрипты для настройки новых узлов рабочего, добавленных в кластер с помощью операций масштабирования. Упорные скрипты не применяются к краям узлов.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Что такое вызовы REST API, чтобы вытащить представление запроса Tez из кластера?

Вы можете использовать следующие конечные точки REST для получения необходимой информации в формате JSON. Используйте основные заголовки аутентификации для обработки запросов.

- Tez Query View: *https:\//\<название кластера>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *\//\<https: cluster name>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Как получить детали конфигурации из кластера ИРЧП с помощью пользователя Active Directory Azure?

Чтобы договориться о надлежащей аутентификации токенов с вашим пользователем AAD, пройти через шлюз, используя следующий формат:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Как использовать API Ambari Restful API для мониторинга производительности YARN?

Если вы называете команду Curl в той же виртуальной сети или в заглянувой виртуальной сети, команда:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Если вы звоните в команду из-за пределов виртуальной сети или из не-одноранговой виртуальной сети, формат команды:

- Для кластера, не относящемуся к ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Для кластера ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl подскажет вам пароль. Необходимо ввести действительный пароль для имени пользователя кластера.

## <a name="billing"></a>Выставление счетов

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Сколько стоит развертывание кластера HDInsight?

Для получения дополнительной информации о ценах и часто задаваемых вопросов, связанных с выставлением счетов, смотрите страницу [ценообразования Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Когда начинается & остановка биллинга В HDInsight?

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Биллинг проражается по минуте.

### <a name="how-do-i-cancel-my-subscription"></a>Как отменить подписку?

Для получения информации об отмене подписки можно [узнать об отмене подписки Azure.](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Что произойдет после отмены подписки с оплатой по мере использования?

Для получения информации о вашей подписке после ее отмены см. [Что произойдет после отмены подписки?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Почему версия Hive отображается как 1.2.1000 вместо 2.1 в uI Ambari, хотя я бегу с кластером HDInsight 3.6?

Хотя только 1.2 появляется в UI Ambari, HDInsight 3.6 содержит как Hive 1.2, так и Hive 2.1.

## <a name="other-faq"></a>Другие часто задаваемые вопросы

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Что предлагает HDInsight с точки зрения возможностей обработки потоков в реальном времени?

Для получения информации о возможностях интеграции обработки потоков в Azure HDInsight [см.](/azure/architecture/data-guide/technology-choices/stream-processing)

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Есть ли способ динамического завершения головного узла кластера, когда кластер простаивает в течение определенного периода?

Вы не можете сделать это с кластерами HDInsight. Для этих сценариев можно использовать фабрику данных Azure.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Какие предложения по соблюдению предлагает HDInsight?

Для получения информации о соответствии требованиям можно ознакомиться в [целевом центре Майкрософт](https://www.microsoft.com/trust-center) и [обзоре соответствия требованиям Microsoft Azure.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)
