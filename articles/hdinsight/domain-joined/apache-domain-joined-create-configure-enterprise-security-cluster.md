---
title: Создание и настройка кластеров пакетов корпоративной безопасности - Azure
description: Узнайте, как создать и настроить кластеры пакетов корпоративной безопасности в Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436029"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Создание и настройка кластеров пакетов корпоративной безопасности в Azure HDInsight

Пакет корпоративной безопасности (ESP) для Azure HDInsight предоставляет доступ к проверке подлинности на основе Active Directory, многопользовательской поддержке и управлению доступом на основе ролей для кластеров Apache Hadoop в Azure. Кластеры HDInsight ESP позволяют организациям, придерживающимися строгих корпоративных политик безопасности, безопасно обрабатывать конфиденциальные данные.

В этом руководстве показано, как создать кластер Azure HDInsight с поддержкой ESP. Он также показывает, как создать Windows IaaS VM, на котором активный каталог и система доменных имен (DNS) включены. Используйте это руководство для настройки необходимых ресурсов, чтобы позволить пользователям на месте войти в кластер HDInsight с поддержкой ESP.

Создаваемый сервер будет служить заменой *вашей реальной* среде. Вы будете использовать его для настройки и конфигурации шагов. Позже вы повторите шаги в своей среде.

Это руководство также поможет вам создать гибридную среду идентификации с помощью синхронизации хэша паролей с Azure Active Directory (Azure AD). Руководство дополняет [использование ESP в HDInsight](apache-domain-joined-architecture.md).

Перед использованием этого процесса в вашей собственной среде:

* Настройка Active Directory и DNS.
* Включить AD Azure.
* Синхронизация учетных записей пользователей с Azure AD.

![Диаграмма архитектуры Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Создание припредствой среды

В этом разделе вы будете использовать шаблон развертывания Azure quickstart для создания новых вс-кодов, настройки DNS и добавления нового леса Active Directory.

1. Перейдите к шаблону развертывания «Быстрый старт», чтобы [создать VM Azure с новым лесом Active Directory.](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. Выберите **Развертывание в Azure**.
1. Войдите в свою подписку Azure.
1. На **сайте «Создайте ВМ» с новой страницей AD Forest** укажите следующую информацию:

    |Свойство | Значение |
    |---|---|
    |Подписка|Выберите подписку, где вы хотите развернуть ресурсы.|
    |Группа ресурсов|Выберите **Создать новый**и ввести имя`OnPremADVRG`|
    |Расположение|Выберите расположение.|
    |Имя входа администратора|`HDIFabrikamAdmin`|
    |Пароль администратора|Введите пароль.|
    |Имя домена|`HDIFabrikam.com`|
    |Префикс Dns|`hdifabrikam`|

    Оставьте остальные значения по умолчанию.

    ![Шаблон для создания Azure VM с новым Azure AD Forest](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Обзор **Условий,** а затем выбрать **Я согласен с условиями, изложенными выше**.
1. Выберите **Покупку**и следите за развертыванием и ждите его завершения. Развертывание занимает около 30 минут.

## <a name="configure-users-and-groups-for-cluster-access"></a>Настройка пользователей и групп для кластерного доступа

В этом разделе к концу этого руководства будут созданы пользователи, которые будут иметь доступ к кластеру HDInsight.

1. Подключайтесь к контроллеру домена с помощью удаленного рабочего стола.
    1. С портала Azure перейдите к **группам** > ресурсов**OnPremADVRG** > **adVM** > **Connect.**
    1. Из списка **выпадающих IP-адресов** выберите общедоступный IP-адрес.
    1. Выберите **Скачать RDP файл,** а затем открыть файл.
    1. Используйте `HDIFabrikam\HDIFabrikamAdmin` в качестве имени пользователя.
    1. Введите пароль, который вы выбрали для учетной записи админ.
    1. Нажмите кнопку **ОК**.

1. С панели мониторинга контроллера домена **Server Manager** переходите к пользователям **и** > **компьютерам Tools Active Directory.**

    ![На панели мониторинга серверного менеджера откройте Active Directory Management](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Создайте двух новых пользователей: **HDIAdmin** и **HDIUser**. Эти два пользователя войдут в кластеры HDInsight.

    1. Со **страницы Активных пользователей каталога и компьютеров,** нажмите правой кнопкой `HDIFabrikam.com`мыши, а затем перейти к **новому** > **пользователю**.

        ![Создание нового пользователя Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. На **новой странице объекта** - `HDIUser` Пользователь введите имя **и** имя **пользователя.** Другие поля будут автозаполнить. Затем выберите **Следующий**.

        ![Создание первого объекта пользователя админ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. В всплывающем окне введите пароль для новой учетной записи. Выберите **Пароль никогда не истекает,** а затем **OK** на всплывающее сообщение.
    1. Выберите **следующий**, а затем **закончить** для создания новой учетной записи.
    1. Повторите вышеуказанные шаги, чтобы создать пользователя. `HDIAdmin`

        ![Создание второго объекта пользователя админ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Создайте глобальную группу безопасности.

    1. От **активных пользователей каталога и компьютеров**, правой кнопкой `HDIFabrikam.com`мыши , а затем перейти к **новой** > **группе**.

    1. Введите `HDIUserGroup` текстовый ящик **названия группы.**

    1. Нажмите кнопку **ОК**.

    ![Создание новой группы Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Создание нового объекта](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Добавление участников в **HDIUserGroup**.

    1. Нажмите **правой кнопкой мыши HDIUser** и выберите **Добавить в группу ...**.
    1. **Введите имена объектов, чтобы** `HDIUserGroup`выбрать текстовое поле, введите . Затем выберите **OK,** и **OK** снова на всплывающее окно.
    1. Повторите предыдущие шаги для учетной записи **HDIAdmin.**

        ![Добавьте члена HDIUser в группу HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Теперь вы создали среду Active Directory. Вы добавили двух пользователей и группу пользователей, которые могут получить доступ к кластеру HDInsight.

Пользователи будут синхронизированы с Azure AD.

### <a name="create-an-azure-ad-directory"></a>Создание каталога Azure AD

1. Войдите на портал Azure.
1. Выберите **Создать** ресурс `directory`и введите . Выберите > **создание** **активного каталога Azure.**
1. Под **названием Организации,** введите `HDIFabrikam`.
1. Под **первоначальным доменным именем**введите `HDIFabrikamoutlook`.
1. Выберите **Создать**.

    ![Создание каталога Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Создание пользовательского домена

1. Из нового **каталога Azure Active Directory**под **управлением**выберите **пользовательские доменные имена.**
1. Выберите **и добавьте пользовательский домен**.
1. Под **пользовательским доменным именем,** введите, `HDIFabrikam.com`а затем выберите **Добавить домен**.
1. Затем завершите [Добавление информации DNS в регистратор домена.](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)

![Создание пользовательского домена](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Создание группы

1. Из нового **каталога Azure Active Directory**под **управлением**выберите **группы.**
1. Выберите **новую группу**.
1. В текстовом поле `AAD DC Administrators` **названия группы** введите .
1. Выберите **Создать**.

## <a name="configure-your-azure-ad-tenant"></a>Настройка арендатора Azure AD

Теперь вы наверстируете свой ad-наниматель Azure, чтобы можно было синхронизировать пользователей и группы от предприимчивого экземпляра Active Directory к облаку.

Создайте администратора арендаторов Active Directory.

1. Вопийте на портале Azure и выберите арендатора Azure **AD, HDIFabrikam.**

1. Перейдите к **управлению** > **пользователями** > **Новый пользователь**.

1. Введите следующие сведения для нового пользователя:

    **идентификации**

    |Свойство |Описание |
    |---|---|
    |Имя пользователя|Введите `fabrikamazureadmin` в текстовое поле. Из списка выпадающих доменных имен выберите`hdifabrikam.com`|
    |name| Введите `fabrikamazureadmin`.|

    **Пароль**
    1. Выберите **Позвольте мне создать пароль**.
    1. Введите безопасный пароль по вашему выбору.

    **Группы и роли**
    1. Выберите **выбранные 0 групп.**
    1. Выберите **администраторов AAD DC,** а затем **выберите**.

    ![Диалоговая коробка групп Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Выберите **пользователя**.
    1. Выберите **глобального администратора,** а затем **выберите**.

    ![Диалогроль роли Ad Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Выберите **Создать**.

1. Затем перейдите нового пользователя на портал Azure, где ему будет предложено изменить пароль. Это необходимо сделать перед настройкой Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Синхронизация пользователей с Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Настройка активного каталога Microsoft Azure Connect

1. С контроллера домена скачайте [Microsoft Azure Active Directory Connect.](https://www.microsoft.com/download/details.aspx?id=47594)

1. Откройте загруженный вами файл и согласитесь на условия лицензии. Выберите **Продолжить**.

1. Выберите **настройки экспресса использования.**

1. На странице **Connect to Azure AD** введите имя пользователя и пароль глобального администратора Azure AD. Используйте имя `fabrikamazureadmin@hdifabrikam.com` пользователя, созданное при настройке арендатора Active Directory. Затем выберите **Следующий**.

    ![Страница "Подключение к Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. На странице **Connect to Active Directory Domain Services** введите имя пользователя и пароль для учетной записи корпоративного админ. Используйте имя `HDIFabrikam\HDIFabrikamAdmin` пользователя и его пароль, созданный ранее. Затем выберите **Следующий**.

   ![Страница "Подключение к Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. На странице **настройки Azure AD войти в систему** выберите **Следующий**.
   ![Страница "Конфигурация входной знак Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. На странице **Ready to configure** выберите **Install**.

   ![Страница "Готов к настройке"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. На странице **Конфигурация завершена** выберите **Выйти**.
   ![Страница "Конфигурация завершена"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. После завершения синхронизации подтвердите, что пользователи, созданные в каталоге IaaS, синхронизированы с Azure AD.
   1. Войдите на портал Azure.
   1. Выберите **Активный каталог** > Azure**HDIFabrikam** > **пользователей.**

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте назначенную пользователем управляемую идентификацию, которую можно использовать для настройки служб домена Azure AD (Azure AD DS). Для получения дополнительной информации [см. Создать, перечислить, удалить или назначить роль управляемому удостоверению пользователя с помощью портала Azure.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

1. Войдите на портал Azure.
1. Выберите **Создать** ресурс `managed identity`и введите . Выберите **Пользователь Назначенный Управляемый Identity** > **Создать**.
1. Для **названия ресурсов,** введите `HDIFabrikamManagedIdentity`.
1. Выберите свою подписку.
1. Под **группой ресурсов**выберите `HDIFabrikam-CentralUS`Создать **новый** и ввести .
1. В **соответствии**с местоположением, выберите **Центральный США**.
1. Выберите **Создать**.

![Создание нового управляемого итогового удостоверения, назначенного пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Включение Azure AD DS

Выполните следующие действия, чтобы включить Azure AD DS. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)

1. Создайте виртуальную сеть для размещения Azure AD DS. Выполнить следующий код PowerShell.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Войдите на портал Azure.
1. Выберите Создать `Domain services` **ресурс,** ввести и выберите **Azure AD доменных служб** > **Создать.**
1. На странице **Основы:**
    1. Под **именем каталога**выберите созданный каталог Azure AD: **HDIFabrikam**.
    1. Для **доменного имени DNS**введите *HDIFabrikam.com*.
    1. Выберите свою подписку.
    1. Укажите ресурсную группу **HDIFabrikam-CentralUS.** Для **расположения**, выберите **Центральный США**.

        ![Основные детали Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. На странице **Сети** выберите сеть **(HDIFabrikam-VNET)** и подсеть **(AADDS-subnet),** созданную с помощью сценария PowerShell. Или выберите **Создать новый** для создания виртуальной сети сейчас.

    ![Шаг «Создание виртуальной сети»](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. На странице **группы администраторов** следует увидеть уведомление о том, что для администрирования этой группы уже создана группа под названием **AAD DC Administrators.** Вы можете изменить членство в этой группе, если хотите, но в этом случае вам не нужно менять ее. Нажмите кнопку **ОК**.

    ![Просмотр группы администраторов Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. На странице **синхронизации** включите полную синхронизацию, выбрав **All** > **OK.**

    ![Включить синхронизацию Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. На странице **Резюме** проверьте сведения для Azure AD DS и выберите **OK**.

    ![Резюме "Включить azure AD доменных служб"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

После включения Azure AD DS локальный DNS-сервер работает на M M Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Наверскакните виртуальную сеть Azure AD DS

Используйте следующие шаги для настройки виртуальной сети Azure AD DS **(HDIFabrikam-AADDSVNET)** для использования пользовательских DNS-серверов.

1. Найдите IP-адреса пользовательских DNS-серверов.
    1. Выберите `HDIFabrikam.com` ресурс Azure AD DS.
    1. Под **управлением**, выберите **Свойства**.
    1. Найти IP-адреса под **IP-адресом в виртуальной сети.**

    ![Найдите пользовательские DNS IP-адреса для Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Нанастройка **HDIFabrikam-AADDSVNET** для использования пользовательских IP-адресов 10.0.0.4 и 10.0.0.5.

    1. В **настройках**выберите **DNS-серверы.**
    1. Выберите **пользовательский**.
    1. В текстовом окне введите первый IP-адрес *(10.0.0.4*).
    1. Нажмите кнопку **Сохранить**.
    1. Повторите шаги, чтобы добавить другой IP-адрес *(10.0.0.5*).

В нашем сценарии мы настроили Azure AD DS для использования IP-адресов 10.0.0.4 и 10.0.0.5, установив тот же IP-адрес в виртуальной сети Azure AD DS:

![Пользовательская страница DNS-серверов](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Обеспечение безопасности трафика LDAP

Легкий протокол доступа к каталогам (LDAP) используется для чтения и записи в Active Directory Azure. Вы можете сделать трафик LDAP конфиденциальным и безопасным с помощью технологии Secure Sockets Layer (SSL) или Transport Layer Security (TLS). Вы можете включить LDAP над SSL (LDAPS) путем установки правильно отформатированный сертификат.

Для получения дополнительной информации о безопасном LDAP [см. Нанастройку LDAPS для домена Azure AD DS.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)

В этом разделе вы создаете сертификат с самостоятельной подписью, загружаете сертификат и настраиваете LDAPS для домена **HDIFabrikam** Azure AD DS.

Следующий скрипт создает сертификат для **HDIFabrikam**. Сертификат сохраняется на пути *LocalMachine.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Любая утилита или приложение, создающее действительный запрос \#на криптографию открытых ключей (PKCS) 10, могут быть использованы для формирования запроса сертификата SSL.

Убедитесь, что сертификат установлен в **личном** магазине компьютера:

1. Запустите консоль управления Майкрософт (MMC).
1. Добавьте snap-in **Сертификаты,** который управляет сертификатами на локальном компьютере.
1. Расширить **сертификаты (местный компьютер)** > **Личные** > **сертификаты**. В **личном** магазине должен быть новый сертификат. Этот сертификат выдается на имя полностью квалифицированного хозяина.

    ![Проверка создания локального сертификата](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. В панели справа щелкните созданный сертификат. Укажите **на все задачи,** а затем выберите **Экспорт**.

1. На странице **Экспорт Частный ключ,** выберите **Да, экспортировать частный ключ**. Компьютер, на котором ключ будет импортирован, нуждается в закрытом ключе для чтения зашифрованных сообщений.

    ![Экспорт Ная ключевая страница Сертификат Экспорт Мастер](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. На странице **формата экспортного файла** оставьте настройки по умолчанию, а затем выберите **следующий**.
1. На странице **Пароль** введите пароль для закрытого ключа. Для **шифрования**выберите **TripleDES-SHA1**. Затем выберите **Следующий**.
1. На странице **«Файл для экспорта»** введите путь и имя для экспортируемого файла сертификата, а затем выберите **Следующий**. Имя файла должно иметь расширение .pfx. Этот файл настроен на портале Azure для создания безопасного соединения.
1. Включите LDAPS для домена Azure AD DS, управляемого DS.
    1. На портале Azure выберите домен. `HDIFabrikam.com`
    1. Под **управлением**, выберите **Безопасный LDAP**.
    1. На странице **Secure LDAP,** под **безопасной LDAP**, выберите **Включить**.
    1. Просмотрите файл сертификата .pfx, который вы экспортировали на компьютере.
    1. Введите пароль сертификата.

    ![Включение защищенного протокола LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Теперь, когда вы включили LDAPS, убедитесь, что он довисот, включив порт 636.
    1. В группе ресурсов **HDIFabrikam-CentralUS** выберите группу сетевой безопасности **AADDS-HDIFabrikam.com-NSG.**
    1. В **настройках**выберите**Add** >  **правила безопасности Входящего.**
    1. На странице **правила безопасности Добавить входящие** введите следующие свойства и **выберите Добавить:**

        | Свойство | Значение |
        |---|---|
        | Источник | Любой |
        | Диапазоны исходных портов | * |
        | Назначение | Любой |
        | Диапазон портов назначения | 636 |
        | Протокол | Любой |
        | Действие | Allow |
        | Приоритет | \<Пожеланное число> |
        | name | Port_LDAP_636 |

    ![Диалог-поле "Добавить входявное правило безопасности"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** — это управляемая личность, назначенная пользователем. Роль contributor Contributor ANDInsight Domain Services включена для управляемой идентификации, которая позволит этой идентификации считывать, создавать, изменять и удалять операции доменных служб.

![Создание управляемого удостоверения, назначаемого пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Создание кластера HDInsight с поддержкой ESP

Этот шаг требует следующих предпосылок:

1. Создайте новую группу ресурсов *HDIFabrikam-WestUS* в месте **западной части США.**
1. Создайте виртуальную сеть, в ней будет размещен кластер HDInsight с поддержкой ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Создайте одноранговые отношения между виртуальной сетью, в хостовой Azure AD DS (`HDIFabrikam-AADDSVNET`) и виртуальной сетью, в размещении кластера HDInsight с поддержкой ESP (`HDIFabrikam-HDIVNet`). Используйте следующий код PowerShell, чтобы заглянуть в две виртуальные сети.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Создайте новую учетную запись Хранения azure Data Lake Gen2 под названием **Hdigen2store.** Налажить учетную запись с управляемым пользователем итог **HDIFabrikamManagedIdentity**. Для получения дополнительной информации [см.](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

1. Настройка пользовательских DNS в виртуальной сети **HDIFabrikam-AADDSVNET.**
    1. Перейдите на портал Azure > **ресурсные группы** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS серверов**.
    1. Выберите **пользовательские** и введите *10.0.0.4* и *10.0.0.5*.
    1. Нажмите кнопку **Сохранить**.

        ![Сохранение пользовательских настроек DNS для виртуальной сети](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Создайте новый кластер HDInsight Spark с поддержкой ESP.
    1. Выберите **пользовательские (размер, настройки, приложения)**.
    1. Введите сведения для **Основы** (раздел 1). Убедитесь, что **тип кластера** **Spark 2.3 (HDI 3.6).** Убедитесь, что **группа ресурсов** **HDIFabrikam-CentralUS.**

    1. Для **сетей безопасности и сетей** (раздел 2) заполните следующие сведения:
        * В **пакете корпоративной безопасности**выберите **Enabled**.
        * Выберите **пользователя кластерного админина** и выберите созданную вами учетную запись **HDIAdmin** в качестве пользователя-предприимчивого админ. Нажмите **Выберите**.
        * Выберите **группу** > доступа кластера**HDIUserGroup.** Любой пользователь, который вы добавите к этой группе в будущем, сможет получить доступ к кластерам HDInsight.

            ![Выберите группу кластерного доступа HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Выполните другие этапы конфигурации кластера и проверьте детали **резюме кластера.** Выберите **Создать**.

1. Восвай в UI Ambari `https://CLUSTERNAME.azurehdinsight.net`для вновь созданного кластера на . Используйте имя `hdiadmin@hdifabrikam.com` пользователя от аминина и его пароль.

    ![Окно ввеком UI Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Из панели мониторинга кластера выберите **роли.**
1. На странице **Роли,** под **присваивать роли этим**, рядом с ролью **администратора кластера,** введите группу *hdiusergroup группы.* 

    ![Назначить роль кластерного админа группе группы hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Откройте клиент Secure Shell (SSH) и вопийте в кластере. Используйте **hdiuser,** созданный в предпосылок Active Directory.

    ![Войти в кластер с помощью клиента SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Если вы можете войти в эту учетную запись, вы правильно настроили кластер ESP для синхронизации с экземпляром Active Directory.

## <a name="next-steps"></a>Дальнейшие действия

Читайте [введение в Apache Hadoop безопасности с ESP](hdinsight-security-overview.md).
