---
title: Создать и настроить кластеры пакет безопасности предприятия в Azure HDInsight
description: Узнайте, как создать и настроить кластеры пакет безопасности предприятия в Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: e9cb9a902cf60fbd3b297a72a7dfa836ee18c835
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484587"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Создать и настроить кластеры пакет безопасности предприятия в Azure HDInsight

Пакет безопасности предприятия для Azure HDInsight предоставляет доступ для проверки подлинности на основе Active Directory, поддержка нескольких пользователей и управление доступом на основе ролей для кластеров Apache Hadoop в Azure. Кластеры HDInsight ESP позволяют организациям, которые соответствуют strict корпоративных политик безопасности, безопасно обрабатывать конфиденциальные данные.

Цель данного руководства — чтобы правильно настроить ресурсы, необходимые, чтобы на предприятии пользователи могут входить ESP кластером HDInsight с поддержкой. В этой статье рассматриваются шаги, которые необходимы для создания кластера Azure HDInsight включен пакет безопасности предприятия. Шаги рассматривается создание виртуальной Машины IaaS Windows с помощью Active Directory и служб доменных имен (DNS) включен. Этот сервер будет выступать в качестве замены для вашего **фактическое** в локальной среде и вы сможете продолжить шаги установки и настройки, чтобы их можно повторить позже в своей среде. В этом руководстве также служит для создания удостоверений в гибридной среде, применяющих синхронизацию хэшей паролей с Azure Active Directory.

Это руководство предназначено в дополнение к [используйте пакет безопасности предприятия в HDInsight](apache-domain-joined-architecture.md)

Прежде чем использовать этот процесс в своей среде, Настройка Active Directory и службы доменных имен (DNS). Кроме того, включите Azure Active Directory и синхронизация локальных учетных записей пользователей в Azure Active Directory.

![схема архитектуры](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Создайте в локальной среде

Рассматриваемые действия: В этом разделе используется шаблон быстрого развертывания Azure для создания новых виртуальных машин, Настройка служб доменных имен (DNS) и нового леса AD.

1. Перейдите к [Создание виртуальной Машины Azure с помощью нового леса AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), чтобы просмотреть шаблон быстрого развертывания.

1. Щелкните **развертывание в Azure**.
1. Войдите в свою подписку Azure.
1. На **Создание виртуальной Машины Azure с помощью нового леса AD** экрана, выполните следующие действия:
    1. Выберите подписку, в котором ресурсы, развертываемые из **подписки** раскрывающегося списка.
    1. Выберите **создать** рядом с полем **группы ресурсов** и введите имя **OnPremADVRG**
    1. Введите указанные ниже сведения для остальной части шаблона поля:

        * **Расположение.** Центральный регион США
        * **Имя администратора**. HDIFabrikamAdmin
        * **Пароль администратора**: < ваш_пароль >
        * **Домен**: HDIFabrikam.com
        * **Префикс DNS**: hdifabrikam

        ![Создание шаблона виртуальной Машины Azure и леса AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Нажмите кнопку **покупки**
    1. Мониторинг развертывания и дождитесь ее завершения.
    1. Подтвердите создание ресурсов в группе ресурсов `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Настройка пользователей и группы для доступа к кластеру

Рассматриваемые действия: В этом разделе вы создадите пользователей, которые будут иметь доступ к кластеру HDInsight к концу в этом руководстве.

1. Подключиться к контроллеру домена, с помощью удаленного рабочего стола.
    1. Если вы использовали шаблон, упомянутых в начале, контроллер домена является виртуальная машина **adVM** в `OnPremADVRG` группы ресурсов.
    1. Перейдите на портал Azure > **групп ресурсов** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Нажмите кнопку **RDP** вкладке и нажмите кнопку **загрузить RDP-файл**.
    1. Сохраните файл на компьютер и откройте его.
    1. При появлении запроса учетных данных, используйте `HDIFabrikam\HDIFabrikamAdmin` как имя пользователя и затем введите пароль, который вы выбрали для учетной записи администратора.

1. Открыв сеанс удаленного рабочего стола на ВМ контроллера домена, запустите **Active Directory — пользователи и компьютеры** из **диспетчера серверов** панели мониторинга. Нажмите кнопку **средства** в правом верхнем углу и затем **Active Directory — пользователи и компьютеры** из раскрывающегося списка.

    ![Управления диспетчера сервера откройте Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Создайте два новых пользователей, **HDIAdmin**, **HDIUser**. Эти два пользователя будет использоваться для входа в кластерах HDInsight.

    1. В **Active Directory — пользователи и компьютеры** щелкните **действие** > **New** > **пользователя**.

        ![Создание нового пользователя Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. В **новый объект — пользователь** введите `HDIUser` как **имя входа пользователя** и нажмите кнопку **Далее**.

        ![Создание первого администратора](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. В появившемся всплывающем окне введите нужный пароль для новой учетной записи. Проверьте поле с текстом **срок действия пароля не ограничен**. HDIClick **ОК**.
    1. Нажмите кнопку **Готово** для создания новой учетной записи.
    1. Создайте другого пользователя `HDIAdmin`.

        ![Создание второго администратора пользователя](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. В **Active Directory — пользователи и компьютеры** щелкните **действие** > **New** > **группы**. Создание `HDIUserGroup` как новая группа.

    ![Создать новую группу Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Создание нового группа2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Добавить **HDIUser** создан на предыдущем шаге, чтобы **HDIUserGroup** как член.

    1. Щелкните правой кнопкой мыши **HDIUserGroup** и нажмите кнопку **свойства**.
    1. Перейдите к **члены** вкладку и нажмите кнопку **добавить**.
    1. Ввод `HDIUser` поле, помеченное **введите имена выбираемых объектов** и нажмите кнопку **ОК**.
    1. Повторите предыдущие шаги для другой учетной записи `HDIAdmin`

        ![Добавление членов в группу](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Вы создали среду Active Directory, а также два пользователя и группы пользователей для доступа к кластеру HDInsight.

Эти пользователи будут синхронизированы с Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Создание нового Azure Active Directory

1. Войдите на портал Azure.
1. Нажмите кнопку **создать ресурс** и тип **directory**. Выберите **Azure Active Directory** > **создание**.
1. Введите **HDIFabrikam** под **имя_организации**.
1. Введите **HDIFabrikamoutlook** под **первоначальное доменное имя**.
1. Нажмите кнопку **Создать**.
1. Слева на портале Azure щелкните **Azure Active Directory**.
1. При необходимости щелкните **переключение каталога** для изменения в новый каталог, который вы создали **HDIFabrikamoutlook**.
1. В разделе **управление** щелкните **имена пользовательских доменов** > **добавить личный домен**.
1. Введите **HDIFabrikam.com** под **пользовательское доменное имя** и нажмите кнопку **добавить домен**.

![Создание нового azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![создать новый пользовательский домен](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Настройка клиента Azure AD

Рассматриваемые действия: Теперь вы настроите вашего клиента Azure AD, таким образом, вы можете синхронизировать пользователей и групп из локальной AD в облаке.

1. Создайте учетную запись администратора клиента AD.
    1. Войдите на портал Azure и выберите свой клиент Azure AD **HDIFabrikam**
    1. Выберите **пользователей** под **управление** и затем **нового пользователя**.
    1. Введите указанные ниже сведения для нового пользователя:

        * Имя: fabrikamazureadmin
        * Имя пользователя: fabrikamazureadmin@hdifabrikam.com
        * Пароль: безопасный пароль по своему усмотрению

    1. Щелкните **группы** раздел, осуществлять поиск **Администраторы контроллера домена AAD**и нажмите кнопку **выберите**.

        ![Группы](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Щелкните **роль каталога** и выберите **глобального администратора** с правой стороны. Нажмите кнопку **ОК**.

        ![Роль каталога](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Введите пароль для пользователя. Нажмите кнопку **Создать**.

1. Если вы хотите изменить пароль для только что созданного пользователя <fabrikamazureadmin@hdifabrikam.com>. Войдите в Azure с помощью удостоверения, а затем будет предложено изменить пароль.

## <a name="sync-on-premises-users-to-azure-ad"></a>Синхронизация локальных пользователей с Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Загрузите и установите Microsoft Azure Active Directory connect

1. [Скачать Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)

1. Установка Microsoft Azure Active Directory connect на контроллере домена.
    1. Откройте исполняемый файл, который вы скачали на предыдущем шаге и соглашаетесь с условиями лицензии. Нажмите кнопку **Продолжить**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Нажмите кнопку **использовать стандартные параметры** и завершите установку.

        ![Использовать стандартные параметры](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Настроить синхронизацию с локальным контроллером домена

1. На **подключение к Azure AD** введите имя пользователя и пароль глобального администратора для Azure AD. Нажмите кнопку **Далее**. Это имя пользователя `fabrikamazureadmin@hdifabrikam.com` , созданную при настройке вашего клиента AD.
    ![Подключение к Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. На **подключение к доменных служб Active Directory** введите имя пользователя и пароль для учетной записи администратора предприятия. Нажмите кнопку **Далее**. Это имя пользователя `HDIFabrikam\HDIFabrikamAdmin` и его соответствующий пароль, который был создан ранее.

   ![Подключение к доменных служб Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. На **Конфигурация входа в Azure AD** щелкните **Далее**.
    ![Конфигурация входа в Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. На все готово для настройки экрана, нажмите кнопку **установить**.
    ![Установка](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Когда **завершить настройку** экран отображается, щелкните **выхода**.
    ![Завершение настройки](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. После завершения синхронизации подтверждение, если пользователи, которые созданы в IAAS Active Directory будут синхронизированы с Azure Active Directory.
    1. Войдите на портал Azure.
    1. Выберите **Azure Active Directory** > **HDIFabrikam** > **пользователей**.

### <a name="create-an-user-assigned-managed-identity"></a>Создание назначаемого пользователем управляемого удостоверения

Создание назначаемого пользователем управляемого удостоверения, будет использоваться для настройки доменных служб Azure Active Directory (Azure AD DS). Дополнительные сведения о создании назначаемого пользователем управляемого удостоверения, см. в разделе [Create, списка "," delete "или" назначить роли для назначаемого пользователем управляемого удостоверения с помощью портала Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Войдите на портал Azure.
1. Нажмите кнопку **создать ресурс** и тип **управляемого удостоверения**. Выберите **пользователем управляемого удостоверения** > **создание**.
1. Введите **HDIFabrikamManagedIdentity** как **имя ресурса**.
1. Выберите свою подписку.
1. В разделе **группы ресурсов** щелкните **создать** и введите **HDIFabrikam CentralUS**.
1. Выберите **Центральная часть США** под **расположение**.
1. Нажмите кнопку **Создать**.

![Создание нового назначаемого пользователем управляемого удостоверения](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Включение доменных служб Azure Active Directory

Дополнительные сведения см. в разделе [Включение доменных служб Azure Active Directory с помощью портала Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Создайте виртуальную сеть к узлу доменных Служб Azure AD. Выполните следующий код powershell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Войдите на портал Azure.
1. Нажмите кнопку **создать ресурс**, введите **доменных служб** и выберите **доменных служб Azure AD**.
1. На **основы** экрана выполните следующие действия:
    1. В разделе **имя каталога** выберите Azure Active Directory, созданные для этой статьи **HDIFabrikam**.
    1. Введите **DNS-имя домена** из **HDIFabrikam.com**.
    1. Выберите свою подписку.
    1. Укажите группу ресурсов **HDIFabrikam CentralUS** и **расположение** из **Центральная часть США**.

        ![Основные сведения в Azure ad ds](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. На **сети** экрана завершения, выберите сеть (**HDIFabrikam-VNET**) и подсети (**AADDS подсети**), созданный с помощью предыдущего скрипта powershell. Или можно использовать **создать** возможность Теперь создайте виртуальную сеть.

    ![Выберите сеть](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. На **группы администраторов** экрана, вы увидите уведомление, в котором создается группа с именем **Администраторы контроллера домена AAD** уже создан для этой группы управления. При необходимости можно изменить членство данной группы, но он не является обязательным для действия в этой статье. Последовательно выберите **ОК**.

    ![представление группы администраторов](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. На **синхронизации** экрана, включите завершения синхронизации, установив **все** и нажмите кнопку **ОК**.

    ![Включение синхронизации](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. На **Сводка** экрана, проверьте сведения для Azure AD DS и нажмите кнопку **ОК**.

    ![Проверка сведений о](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. После включения доменных служб Azure AD локальный сервер службы доменных имен (DNS) будет запущен на виртуальных машинах AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Настроить виртуальную сеть доменных Служб Azure AD

Действия, описанные в этом разделе поможет вам настроить виртуальную сеть доменных Служб Azure AD (**HDIFabrikam AADDSVNET**) для использования пользовательского DNS-серверы.

1. Найдите IP-адреса пользовательского DNS-серверов. Щелкните **HDIFabrikam.com** ресурсов AD DS, нажмите кнопку **свойства** под **управление**   и посмотрите на IP-адреса, перечисленные в разделе **IP-адрес Адрес в виртуальной сети**.

    ![Найдите пользовательский IP-адреса DNS для Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Настройка **HDIFabrikam AADDSVNET** для пользовательских IP-адресов `10.0.0.4` и `10.0.0.5`.

    1. Выберите **DNS-серверы** под **параметры** категории. Щелкните переключатель рядом с полем **Custom**, введите в текстовом поле ниже первый IP-адрес (10.0.0.4) и нажмите кнопку **Сохранить**.
    1. Добавьте дополнительные IP-адреса (10.0.0.5) используя те же шаги.

1. В нашем сценарии доменных Служб Azure AD были настроены для использования IP-адреса 10.0.0.4 и 10.0.0.5, задавая одинаковые IP-адрес в виртуальной сети AADDS, как показано на рисунке ниже.

    ![Просмотр пользовательских DNS-серверы](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Обеспечение безопасности трафика LDAP

Упрощенный Directory Access Protocol (LDAP) используется для чтения и записи в Active Directory. Можно сделать LDAP трафика конфиденциальности и безопасности с помощью Secure Sockets Layer (SSL) и технологии безопасности транспортного уровня (TLS). Можно включить LDAP через SSL (LDAPS), установив сертификат в правильном формате.

Дополнительные сведения о защищенного протокола LDAP, см. в разделе [Настройка защищенного протокола LDAP (LDAPS) для доменных служб Azure AD управляемого домена](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

В этом разделе создайте самозаверяющий сертификат, загрузите сертификат и настройка защищенного протокола LDAP (LDAPS) для **hdifabrikam** доменных Служб Azure AD управляемого домена.

Следующий скрипт создает сертификат для hdifabrikam. Сертификат сохраняется по пути «LocalMachine».

> [!Note] 
> Любые программы или приложение, которое создает допустимый PKCS \#10 запроса можно использовать для формирования запроса на сертификат SSL.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Убедитесь, что сертификат установлен на компьютере\'s личном хранилище. Затем сделайте следующее:

1. Запустите консоль управления (MMC).
1. Добавьте оснастку Сертификаты и управляет сертификатов на локальном компьютере.
1. Разверните **сертификаты (локальный компьютер)** , разверните **личные**, а затем разверните **сертификаты**. Новый сертификат должен существовать в личном хранилище. Этот сертификат выпускается для полного доменного имени узла.

    ![Проверка создания сертификата](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. В области справа щелкните правой кнопкой мыши сертификат, созданный на предыдущем шаге, пункты **все задачи**, а затем нажмите кнопку **Экспорт**.

1. На **Экспорт закрытого ключа** щелкните **Да, экспортировать закрытый, ключ**. Закрытый ключ является обязательным для зашифрованных сообщений для чтения с компьютера, где будет импортировать ключ.

    ![Экспорт закрытого ключа](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. На **Формат экспортируемого файла** странице оставьте параметры по умолчанию и нажмите кнопку **Далее**. 
1. На **пароль** введите пароль для закрытого ключа, выберите **TripleDES SHA1** для **шифрования** и нажмите кнопку **Далее**.
1. На **экспортируемого файла** странице, введите путь и имя файла экспортированного сертификата и нажмите кнопку **Далее**.
1. Имя файла должно иметь расширение PFX, этот файл настроен на портале Azure, чтобы установить безопасное подключение.
1. Включение защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD.
    1. Выберите домен **HDIFabrikam.com** на портале Azure.
    1. Нажмите кнопку **защищенного протокола LDAP** под **управление**.
    1. На **защищенного протокола LDAP** щелкните **включить** под **защищенного протокола LDAP**.
    1. Найдите PFX-файл сертификата, который был экспортирован на компьютере.
    1. Введите пароль сертификата.

    ![Включение защищенного протокола ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Теперь, когда вы включили защищенный протокол LDAP, убедитесь, что она была доступна, включив порт 636.
    1. Выберите группу безопасности сети **AADDS-HDIFabrikam.com-NSG** в **HDIFabrikam CentralUS** группы ресурсов.
    1. В разделе **параметры** щелкните **правила безопасности для входящего трафика** > **добавить**.
    1. На **добавить правило безопасности для входящего трафика** экрана, введите следующие свойства и нажмите кнопку **добавить**:

        | Свойство | Значение |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Место назначения | Any |
        | Destination port range | 636 |
        | Протокол | Any |
        | Action | Allow |
        | Приоритет | <Desired Number> |
        | Name | Port_LDAP_636 |

    ![правило безопасности для входящего трафика](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` — назначаемого пользователем управляемого удостоверения, роль участника службы HDInsight домена включен для управляемого удостоверения, который позволит это удостоверение для чтения, создание, изменение и удаление операций службы домена.

    ![Создание пользовательского управляемого удостоверения](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Создание пакета безопасности предприятия включена кластера HDInsight

На этом этапе требуется следующие предварительные требования:

1. Создать новую группу ресурсов `HDIFabrikam-WestUS` в расположении `West US`.
1. Создание виртуальной сети, где будет размещаться ESP включена кластера HDInsight.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Создание одноранговой связи между виртуальной сети, на котором размещается AADDS (`HDIFabrikam-AADDSVNET`) и кластером HDInsight с поддержкой виртуальной сети, где будет размещаться ESP (`HDIFabrikam-HDIVNet`). Используйте следующий код powershell, чтобы между этими двумя виртуальными сетями.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Создать новую учетную запись Azure Data Lake хранилища Gen2 **Hdigen2store**, в котором настроено следующее удостоверение управляемое пользователем **HDIFabrikamManagedIdentity**. Дополнительные сведения о создании учетных записей Gen2 хранилища Озера данных с пользователем управляемые удостоверения, см. в разделе [кластеров Gen2 хранилища Озера данных Azure используйте с Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Настройка пользовательской службы DNS на **HDIFabrikam AADDSVNET** виртуальной сети.
    1. Перейдите на портал Azure > **групп ресурсов** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >   **DNS-серверы**.
    1. Выберите **Custom** и введите `10.0.0.4` и `10.0.0.5`.
    1. Выберите команду **Сохранить**.

        ![сохранить параметры пользовательской службы dns](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Создание нового кластера ESP с поддержкой HDInsight Spark.
    1. Нажмите кнопку **Custom (размер, параметры, приложения)** .
    2. Введите нужные сведения о разделе 1 **основы**. Убедитесь, что **тип кластера** — **Spark 2.3 (HDI 3.6)** и **группы ресурсов** — **HDIFabrikam-CentralUS**

    1. В разделе 2 **безопасность и сети**, выполните следующие действия:
        1. Нажмите кнопку **включена** под **пакет безопасности предприятия**.
        1. Нажмите кнопку **администратора кластера** и выберите **HDIAdmin** учетную запись, созданную ранее как учетную запись локального администратора. Нажмите кнопку **Выбрать**.

        1. Нажмите кнопку **группы доступа кластера** , а затем выберите **HDIUserGroup**. Любой пользователь, добавленный в эту группу, в будущем будет возможность доступа к кластерам HDInsight.

            ![Выберите группу доступа кластера](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Выполните остальные шаги конфигурации кластера и проверьте сведения на **Сводка кластера**. Нажмите кнопку **Создать**.

1. Войдите в пользовательский интерфейс Ambari для только что созданный кластер в `https://CLUSTERNAME.azurehdinsight.net` , используя имя пользователя администратора `hdiadmin@hdifabrikam.com` и пароль.

    ![Войдите в Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Нажмите кнопку **ролей** из панели мониторинга кластера.
1. На **ролей** введите группе **hdiusergroup** их назначить **администратор кластера** роль в рамках **назначить эти роли**.

    ![назначить роль администратора кластера hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Откройте ваш клиент SSH и войдите в кластер с помощью **hdiuser** , созданную ранее в локальной Active Directory.

    ![Имя входа к кластеру с помощью SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Если по этой учетной записи, затем кластера ESP правильно настроен для синхронизации с локальной службой active directory.

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения об обеспечении безопасности Apache Hadoop с помощью Корпоративного пакета безопасности](apache-domain-joined-introduction.md)
