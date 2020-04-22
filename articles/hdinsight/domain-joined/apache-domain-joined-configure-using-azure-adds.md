---
title: Корпоративная безопасность с Azure AD DS - Azure HDInsight
description: Узнайте, как настроить и настроить кластер пакетов безопасности предприятия HDInsight, используя службы домена Azure Active Directory Domain.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/17/2020
ms.openlocfilehash: c045378b6e69a9bb1b696d3390dadf84a50bd3b7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687272"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Конфигурации пакета корпоративной безопасности с помощью служб домена Active Directory в HDInsight

Кластеры Enterprise Security Package (ESP) обеспечивают многопользовательский доступ к кластерам Azure HDInsight. Кластеры HDInsight с ESP подключены к домену. Это соединение позволяет пользователям доменов использовать свои учетные данные домена для проверки подлинности с кластерами и выполнения заданий больших данных.

В этой статье вы узнаете, как настроить кластер HDInsight с ПОМОЩЬю службы доменов Active Directory (Azure AD DS).

> [!NOTE]  
> ESP, как правило, доступен в HDInsight 3.6 и 4.0 для этих типов кластеров: Apache Spark, Interactive, Hadoop и HBase. ESP для кластера типа Apache Kafka находится в предварительном просмотре только с поддержкой с наилучшими усилиями. Кластеры ESP, созданные до даты ESP GA (1 октября 2018 г.), не поддерживаются.

## <a name="enable-azure-ad-ds"></a>Включение Azure AD DS

> [!NOTE]  
> Только администраторы-арендаторы имеют привилегии для включения DS Azure AD. Если кластерное хранилище — это хранилище Azure Data Lake Storage Gen1 или Gen2, необходимо отключить многофакторную аутентификацию Azure только для пользователей, которым потребуется доступ к кластеру с помощью базовой аутентификации Kerberos.
>
> Доверенные [IP-адреса](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) или [условный доступ](../../active-directory/conditional-access/overview.md) для отключить многофакторную аутентификацию для конкретных пользователей *можно только* при доступе к диапазону IP для виртуальной сети кластера HDInsight. Если вы используете Условный доступ, убедитесь, что служба Active Directory включена в виртуальную сеть HDInsight.
>
> Если хранилище кластера является хранилищем Azure Blob, не отменяйте многофакторную аутентификацию.

Включение Azure AD DS является необходимым условием, прежде чем вы сможете создать кластер HDInsight с ESP. Для получения дополнительной информации [см.](../../active-directory-domain-services/tutorial-create-instance.md)

При включении DD Azure все пользователи и объекты начинают синхронизировать сяпотворство от Active Directory Azure (Azure AD) до DD Azure AD по умолчанию. Продолжительность операции синхронизации зависит от числа объектов в Azure AD. Синхронизация может занять несколько дней для сотен тысяч объектов.

Доменное имя, которое используется с Azure AD DS, должно быть 39 символов или меньше, чтобы работать с HDInsight.

Вы можете синхронизировать только те группы, которые нуждаются в доступе к кластерам HDInsight. Этот вариант синхронизации только определенных групп называется *синхронизацией определенных объектов*. Для инструкций [см. синхронизацию с областью синхронизации С Azure AD в управляемом домене.](../../active-directory-domain-services/scoped-synchronization.md)

Когда вы позволяете безопасно едить LDAP, поместите доменное имя в имя объекта съемки. И тема альтернативное название в сертификате. Если ваше доменное имя *contoso100.onmicrosoft.com,* убедитесь, что точное имя существует в вашем имени объекта сертификата и альтернативном имени субъекта. Дополнительные сведения см. в разделе [Настройка защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Следующий пример создает сертификат, подписанный самостоятельно. Доменное имя *contoso100.onmicrosoft.com* `Subject` находится как в `DnsName` (имя субъекта) и (субъект альтернативное имя).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Проверка состояния здоровья Azure AD DS

Просмотр состояния работоспособности служб домена Active Directory, выбрав **«Здоровье»** в категории **«Управление».** Убедитесь, что статус Azure AD DS является зеленым (запущенным) и синхронизация завершена.

![Здоровье Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Создание и авторизация управляемого удостоверения

Используйте *назначенную пользователем управляемую идентификацию* для упрощения операций защищенных доменных служб. При присвоении роли **вкладчика доменов HDInsight** — управляемой идентификации он может читать, создавать, изменять и удалять операции служб домена.

Некоторые операции доменных служб, такие как создание операционной компании и основ обслуживания, необходимы для пакета безопасности предприятия HDInsight. Можно создавать управляемые идентификаторы в любой подписке. Для получения дополнительной информации об управляемых идентификаторах в целом [см.](../../active-directory/managed-identities-azure-resources/overview.md) Более подробную информацию о том, как работают управляемые идентификаторы в Azure HDInsight, можно узнать [в Azure HDInsight.](../hdinsight-managed-identities.md)

Чтобы настроить кластеры ESP, создайте управляемое удостоверение, назначенное пользователем, если его у вас еще нет. Смотрите [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Затем присвоите роль **вкладчика доменных служб HDInsight** в управляемой идентификации в **управлении доступом** для Azure AD DS. Для выполнения этого назначения для выполнения этой задачи необходимо привилегий AD-ad-ad.

![Служба контроля доступа доменных служб Azure Active Directory](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Назначение роли **вкладчика служб доменов HDInsight** гарантирует, что эта идентификация имеет надлежащий ()`on behalf of`доступ к работе доменных служб на домене Azure AD DS. Эти операции включают создание и удаляние ОУ.

После присвоения управляемой идентификации роль, админ Azure AD управляет тем, кто им пользуется. Во-первых, админ выбирает управляемую идентификацию на портале. Затем выберите **контроль доступа (IAM)** под **обзором**. Админ назначает роль **оператора управляемой идентификации** пользователям или группам, которые хотят создать кластеры ESP.

Например, админ Azure AD DS может назначить эту роль группе **MarketingTeam** для управляемой итог **sjmsi.** Пример показан на следующем рисунке. Это назначение гарантирует, что нужные люди в организации могут использовать управляемый итог для создания кластеров ESP.

![Назначение роли оператора управляемого удостоверения HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Рекомендации по сети

> [!NOTE]  
> DS Azure AD должен быть развернут в виртуальной сети на основе ресурсов Azure Manager. Классические виртуальные сети не поддерживаются для Azure AD DS. Для получения дополнительной информации [см.](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)

Включить Azure Ad DS. Затем локальный сервер Domain Name System (DNS) работает на виртуальных машинах Active Directory (VMs). Настроили виртуальную сеть Azure AD DS для использования этих пользовательских DNS-серверов. Чтобы найти правильные IP-адреса, выберите **Свойства** в категории **Управление** и посмотрите под **IP ADDRESS ON VIRTUAL NETWORK**.

![Поиск IP-адресов для локальных DNS-серверов](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Измените конфигурацию DNS-серверов в виртуальной сети Azure AD DS. Чтобы использовать эти пользовательские iPs, выберите **DNS-серверы** в **категории "Настройки".** Затем выберите опцию **Custom,** введите первый IP-адрес в текстовом поле и выберите **Сохранить**. Добавьте больше IP-адресов, используя те же шаги.

![Обновление конфигурации виртуальной сети DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Проще разместить экземпляр Azure AD DS и кластер HDInsight в одной и той же виртуальной сети Azure. Если вы планируете использовать различные виртуальные сети, вы должны заглянуть эти виртуальные сети, так что контроллер домена виден HDInsight VMs. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md).

После того, как виртуальные сети будут заглянены, настроите виртуальную сеть HDInsight для использования пользовательского DNS-сервера. И введите частные IPs Azure AD DS по мере адреса DNS-сервера. Когда обе виртуальные сети используют одни и те же DNS-серверы, пользовательское доменное имя будет разбираться в нужном IP и будет доступно с HDInsight. Например, если ваше `contoso.com`доменное имя, `ping contoso.com` то после этого шага, должны решить вправо Azure AD DS IP.

![Настройка пользовательских DNS-серверов для виртуальной сети](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Если вы используете правила группы сетевой безопасности (NSG) в подсети HDInsight, следует разрешить [необходимые ИС](../hdinsight-management-ip-addresses.md) как для входящего, так и для исходящего трафика.

Чтобы протестировать настройку сети, присоединяйтесь к Windows VM в виртуальную сеть/подсеть HDInsight и пинги тедоменное имя. (Он должен решить с IP.) Запустите **ldp.exe** для доступа к домену Azure AD DS. Затем присоединяйтесь к этому Windows VM к домену, чтобы подтвердить, что все необходимые вызовы RPC преуспевают между клиентом и сервером.

Используйте **nslookup** для подтверждения доступа сети к вашей учетной записи хранения. Или любую внешнюю базу данных, которую вы могли бы использовать (например, внешний метамагазин Hive или Ranger DB). Убедитесь, что [необходимые порты](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) разрешены в правилах NSG подсети Azure AD DS, если NSG обеспечивает безопасность Azure AD DS. Если соединение домена с этим Windows VM успешно, то вы можете продолжить следующий шаг и создать кластеры ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Создание кластера HDInsight с помощью ESP

После правильной настройки предыдущих шагов следующим шагом является создание кластера HDInsight с включенным ESP. При создании кластера HDInsight можно включить пакет корпоративной безопасности на сетевой вкладке **«Безопасность и сеть».** Для развертывания шаблона управления ресурсами Azure для развертывания используйте интерфейс портала один раз. Затем загрузите заранее заполненный шаблон в **Обзоре и создайте** страницу для повторного использования в будущем.

Вы также можете включить функцию [HDInsight ID Broker](identity-broker.md) во время создания кластера. Функция ID Broker позволяет войти в Ambari с помощью Multi-Factor Authentication и получить необходимые билеты Kerberos без необходимости хэши паролей в Azure AD DS.

> [!NOTE]  
> Первые шесть символов имени кластера ESP должно быть уникальными в вашей среде. Например, если у вас есть несколько кластеров ESP в разных виртуальных сетях, выберите конвенцию именования, которая гарантирует, что первые шесть символов в именах кластеров уникальны.

![Проверка домена для пакета безопасности предприятия Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

После включения ESP автоматически обнаруживаются и проверяются распространенные неправильные настройки, связанные с DS Azure AD. После исправления этих ошибок можно продолжить следующий шаг.

![Пакет безопасности предприятия Azure HDInsight не удалось проверки домена](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

При создании кластера HDInsight с ESP необходимо предоставить следующие параметры:

* **Пользователь кластерного админ:** Выберите админ для кластера из синхронизированного экземпляра Azure AD DS. Эта учетная запись домена должна быть уже синхронизирована и доступна в Azure AD DS.

* **Группы кластерного доступа:** Группы безопасности, пользователи которых необходимо синхронизировать и иметь доступ к кластеру, должны быть доступны в Azure AD DS. Примером может быть группа HiveUsers. Дополнительные сведения см. в статье [Создание группы и добавление в нее пользователей в Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **LDAPS URL**: `ldaps://contoso.com:636`Пример .

Созданная управляемость может быть выбрана из **списка выпадающих управляемых удостоверений личности, назначенных пользователем,** при создании нового кластера.

![Azure HDInsight ESP Active Directory Domain Services управляла имитаторами](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Следующие шаги

* Сведения о настройке политик Hive и выполнении запросов Hive см. в статье [Настройка политик Apache Hive в HDInsight с Корпоративным пакетом безопасности](apache-domain-joined-run-hive.md).
* Сведения о подключении к кластерам HDInsight с корпоративным пакетом безопасности с использованием SSH см. в разделе [Проверка подлинности при использовании присоединенного к домену кластера HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
