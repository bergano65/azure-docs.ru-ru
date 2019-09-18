---
title: Создание и настройка кластеров Корпоративный пакет безопасности в Azure HDInsight
description: Узнайте, как создавать и настраивать кластеры Корпоративный пакет безопасности в Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 482e3e534486a226783e2377c10b074a087e67ed
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037301"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Создание и настройка кластеров Корпоративный пакет безопасности в Azure HDInsight

Корпоративный пакет безопасности для Azure HDInsight предоставляет доступ к аутентификации на основе Active Directory, поддержке нескольких пользователей и управлению доступом на основе ролей для кластеров Apache Hadoop в Azure. Кластеры HDInsight ESP позволяют организациям безопасно обрабатывать конфиденциальные данные с учетом строгой корпоративной политики безопасности.

Цель этого руководством — правильно настроить необходимые ресурсы, чтобы локальные пользователи могли входить в кластер HDInsight с поддержкой ESP. В этой статье рассматриваются шаги, необходимые для создания кластера Azure HDInsight с поддержкой Корпоративный пакет безопасности. Эти действия посвящены созданию виртуальной машины IaaS Windows с Active Directory & включенными службами доменных имен (DNS). Этот сервер будет использоваться в качестве замены для **реальной** локальной среды и позволит вам выполнить действия по настройке и настройке, чтобы вы могли повторить их позже в своей среде. Это также поможет вам создать гибридную среду удостоверений с помощью синхронизации хэшей паролей с Azure Active Directory.

Это руководством предназначено для дополнения [использования корпоративный пакет безопасности в HDInsight](apache-domain-joined-architecture.md)

Прежде чем использовать этот процесс в собственной среде, Настройте Active Directory и службы доменных имен (DNS). Кроме того, включите Azure Active Directory и синхронизируйте локальные учетные записи пользователей для Azure Active Directory.

![Схема архитектуры Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-on-premises-environment"></a>Создание локальной среды

Рассматриваемые действия: В этом разделе вы будете использовать шаблон быстрого развертывания Azure для создания новых виртуальных машин, настройки служб доменных имен (DNS) и нового леса AD.

1. Чтобы просмотреть шаблон быстрого развертывания, перейдите к разделу [Создание виртуальной машины Azure с помощью нового леса AD](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Щелкните **развернуть в Azure**.
1. Войдите в свою подписку Azure.
1. На экране **Создание виртуальной машины Azure с помощью нового леса AD** выполните следующие действия.
    1. Выберите подписку, в которой необходимо развернуть ресурсы из раскрывающегося списка **подписки** .
    1. Выберите **создать** рядом с **группой ресурсов** и введите имя **онпремадврг** .
    1. Введите следующие сведения для остальных полей шаблона:

        * **Расположение.** Центральная часть США
        * **Имя администратора**. хдифабрикамадмин
        * **Пароль администратора**: < YOUR_PASSWORD >
        * **Домен**: HDIFabrikam.com
        * **Префикс DNS**: хдифабрикам

        ![Создание шаблона виртуальной машины Azure и леса AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Щелкните **купить** .
    1. Отслеживая развертывание, дождитесь его завершения.
    1. Убедитесь, что ресурсы созданы в правильной группе `OnPremADVRG`ресурсов.

## <a name="configure-users-and-groups-for-cluster-access"></a>Настройка пользователей и групп для доступа к кластеру

Рассматриваемые действия: В этом разделе вы создадите пользователей, которые будут иметь доступ к кластеру HDInsight, в конце этого руководством.

1. Подключитесь к контроллеру домена с помощью удаленный рабочий стол.
    1. Если вы использовали шаблон, упомянутый в начале, то контроллер домена — это виртуальная машина с именем `OnPremADVRG` адвм в группе ресурсов.
    1. Перейдите в портал Azure > **группы** > ресурсов**онпремадврг** > **адвм** > **Connect**.
    1. Перейдите на вкладку **RDP** и нажмите кнопку **скачать RDP-файл**.
    1. Сохраните файл на компьютере и откройте его.
    1. При появлении запроса на ввод `HDIFabrikam\HDIFabrikamAdmin` учетных данных используйте в качестве имени пользователя и введите пароль, выбранный для учетной записи администратора.

1. После открытия сеанса удаленный рабочий стол на виртуальной машине контроллера домена запустите **Active Directory пользователи и компьютеры** из панели мониторинга **Диспетчер сервера** . Щелкните **инструменты** в правом верхнем углу, а затем **Active Directory пользователи и компьютеры** из раскрывающегося списка.

    ![диспетчер сервера открыть Active Directory управление](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Создайте два новых пользователя: **хдиадмин**, **HDIUser**. Эти два пользователя будут использоваться для входа в кластеры HDInsight.

    1. На экране **Active Directory пользователи и компьютеры** выберите **действие** > **Новый** > **пользователь**.

        ![Создание нового Active Directory пользователя](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. На экране **новый объект — пользователь** введите `HDIUser` **имя пользователя для входа** и нажмите кнопку **Далее**.

        ![Создание первого объекта пользователя "Администратор"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. В появившемся всплывающем окне введите нужный пароль для новой учетной записи. Установите флажок **срок действия пароля никогда не истекает**. Хдикликк **ОК**.
    1. Нажмите кнопку **Готово** , чтобы создать новую учетную запись.
    1. Создайте другого пользователя `HDIAdmin`.

        ![Создание второго объекта пользователя "Администратор"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. На экране **Active Directory пользователи и компьютеры** щелкните **действие** > **создать** > **группу**. Создать `HDIUserGroup` как новую группу.

    ![Создать новую группу Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![диалоговое окно создания новой группы объектов](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Добавьте **HDIUser** , созданный на предыдущем шаге, в **хдиусерграуп** в качестве члена.

    1. Щелкните правой кнопкой мыши **хдиусерграуп** и выберите пункт **свойства**.
    1. Перейдите на вкладку **члены** и нажмите кнопку **Добавить**.
    1. Введите `HDIUser` в поле **Введите имена объектов для выбора** и нажмите кнопку **ОК**.
    1. Повторите предыдущие шаги для другой учетной записи.`HDIAdmin`

        ![Добавление члена HDIUser в группу Хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Теперь вы создали среду Active Directory, а также два пользователя и группу пользователей для доступа к кластеру HDInsight.

Эти пользователи будут синхронизированы с Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Создание нового Azure Active Directory

1. Войдите на портал Azure.
1. Щелкните **создать ресурс** и введите **Каталог**. Выберите **Azure Active Directory** > **создать**.
1. Введите **хдифабрикам** в поле **название организации**.
1. Введите **хдифабрикамаутлук** в поле **имя исходного домена**.
1. Нажмите кнопку **Создать**.
1. В левой части портал Azure щелкните **Azure Active Directory**.
1. При необходимости щелкните **Сменить каталог** , чтобы перейти в новый каталог, созданный **хдифабрикамаутлук**.
1. В разделе **Управление** щелкните **пользовательские доменные имена** > **Добавить личный домен**.
1. Введите **HDIFabrikam.com** в поле **имя пользовательского домена** и нажмите кнопку **Добавить домен**.

![Создание новой службы Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Azure AD, создание нового пользовательского домена](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Настройка клиента Azure AD

Рассматриваемые действия: Теперь вы настроите клиент Azure AD, чтобы вы могли синхронизировать пользователей и группы из локальной службы AD в облако.

1. Создайте администратора клиента Active Directory.
    1. Войдите в портал Azure и выберите свой клиент Azure AD **хдифабрикам** .
    1. Выберите **Пользователи** в разделе **Управление** , а затем — **Новый пользователь**.
    1. Введите следующие сведения для нового пользователя:

        * Имя: фабрикамазуреадмин
        * Имя пользователя:fabrikamazureadmin@hdifabrikam.com
        * Пароль: защищенный пароль по своему усмотрению.

    1. Щелкните раздел **группы** , найдите " **Администраторы контроллера домена AAD**" и нажмите кнопку " **выбрать**".

        ![Диалоговое окно групп Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Щелкните раздел **роль каталога** и в правой части выберите **глобальный администратор** . Нажмите кнопку **ОК**.

        ![Диалоговое окно роли Azure Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Введите пароль для пользователя. Нажмите кнопку **Создать**.

1. Если вы хотите изменить пароль для созданного пользователя <fabrikamazureadmin@hdifabrikam.com>. Войдите в портал Azure с помощью удостоверения, после чего вам будет предложено изменить пароль.

## <a name="sync-on-premises-users-to-azure-ad"></a>Синхронизация локальных пользователей с Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Скачивание и установка Microsoft Azure Active Directory подключения

1. [Скачать Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)

1. Установите Microsoft Azure Active Directory подключиться на контроллере домена.

    1. Откройте исполняемый файл, скачанный на предыдущем шаге, и примите условия лицензии. Нажмите кнопку **Продолжить**.

        ![Добро пожаловать в диалоговое окно Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Щелкните **использовать Экспресс параметры** и завершите установку.

        ![Параметры Azure AD Connect Express](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Настройка синхронизации с локальным контроллером домена

1. На экране **Подключение к Azure AD** введите имя пользователя и пароль глобального администратора для Azure AD. Нажмите кнопку **Далее**. Это имя пользователя `fabrikamazureadmin@hdifabrikam.com` , созданное при настройке клиента AD.

    ![Подключение к Azure Active Directory ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. На экране **Подключение к домен Active Directory службам** введите имя пользователя и пароль для учетной записи администратора предприятия. Нажмите кнопку **Далее**. Это имя пользователя `HDIFabrikam\HDIFabrikamAdmin` и соответствующий пароль, который вы создали ранее.

   ![Подключение к службам домен Active Directory Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. На странице **Настройка входа в Azure AD** нажмите кнопку **Далее**.
    ![Конфигурация входа в Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. На экране все готово для настройки нажмите кнопку **установить**.

    ![Azure AD Connect готово к настройке диалогового окна](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Когда появится экран **Конфигурация завершена** , нажмите кнопку **выход**.
    ![Настройка Azure AD завершена](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. После завершения синхронизации убедитесь, что пользователи, созданные на Active Directory IAAS, синхронизируются с Azure Active Directory.
    1. Войдите на портал Azure.
    1. Выберите **Azure Active Directory** > хдифабрикампользователей > .

### <a name="create-an-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначенного пользователем

Создайте управляемое пользователем удостоверение, которое будет использоваться для настройки доменных служб Azure Active Directory (Azure AD-DS). Дополнительные сведения о создании управляемого удостоверения, назначаемого пользователем, см. [в разделе Создание, перечисление, удаление или назначение роли назначенному пользователем управляемому удостоверению с помощью портал Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Войдите на портал Azure.
1. Щелкните **создать ресурс** и введите **управляемое удостоверение**. Выберите > **Создание** **управляемого удостоверения, назначенное пользователем**.
1. Введите **хдифабрикамманажедидентити** в качестве **имени ресурса**.
1. Выберите свою подписку.
1. В разделе **Группа ресурсов** щелкните **создать** и введите **хдифабрикам-CentralUS**.
1. Выберите **Центральная американская** в разделе **Расположение**.
1. Нажмите кнопку **Создать**.

![Создание нового управляемого удостоверения, назначенного пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Включение доменных служб Azure Active Directory

Дополнительные сведения см. в разделе [Включение доменных служб Azure Active Directory с помощью портала Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Создайте виртуальную сеть для размещения Azure AD-DS. Выполните следующий код PowerShell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Войдите на портал Azure.
1. Щелкните **создать ресурс**, введите **доменные службы** и выберите **доменные службы Azure AD**.
1. На экране **Основные сведения** выполните следующие действия.
    1. В разделе **имя каталога** выберите Azure Active Directory, созданную для этой статьи **хдифабрикам**.
    1. Введите **DNS-имя домена** **HDIFabrikam.com**.
    1. Выберите свою подписку.
    1. Укажите группу ресурсов **хдифабрикам-CentralUS** и **Расположение** **центральной части США**.

        ![Основные сведения о доменных службах Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. На экране **сети** выберите сеть (**хдифабрикам-vnet**) и подсеть (**AADDS-Subnet**), созданную с помощью предыдущего сценария PowerShell. Кроме того, можно использовать параметр **создать** , чтобы создать виртуальную сеть прямо сейчас.

    ![Создание виртуальной сети в доменных службах Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. На экране **группы администраторов** появится уведомление о том, что группа с именем **Администраторы контроллера домена AAD** уже создана для администрирования этой группы. При необходимости можно изменить членство в этой группе, но это не является обязательным для выполнения действий, описанных в этой статье. Нажмите кнопку **ОК**.

    ![Просмотр группы администраторов Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. На экране **Синхронизация** Включите полную синхронизацию, выбрав **все** и нажмите кнопку **ОК**.

    ![Включение синхронизации доменных служб Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. На экране **Сводка** проверьте сведения для Azure AD-DS и нажмите кнопку **ОК**.

    ![Включить сводку доменных служб Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. После включения доменных служб Azure AD локальный сервер службы доменных имен (DNS) будет запущен на виртуальных машинах AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Настройка виртуальной сети Azure AD DS

Действия, описанные в этом разделе, помогут настроить виртуальную сеть Azure AD DS (**хдифабрикам-ааддсвнет**) для использования пользовательских DNS-серверов.

1. Нахождение IP-адресов пользовательских DNS-серверов. Щелкните ресурс **HDIFabrikam.com** AD DS, щелкните **свойства** в разделе **Управление**   и просмотрите IP-адреса в списке **IP-адрес в виртуальной сети**.

    ![Поиск настраиваемых IP-адресов DNS для Azure AD — DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Настройте **хдифабрикам-ааддсвнет** в настраиваемых `10.0.0.4` адресах IP и `10.0.0.5`.

    1. В категории **Параметры** выберитеDNS- серверы. затем щелкните переключатель рядом с полем **Настраиваемый**, введите первый IP-адрес (10.0.0.4) в текстовом поле ниже и нажмите кнопку **сохранить**.
    1. Добавьте дополнительные IP-адреса (10.0.0.5), выполнив те же действия.

1. В нашем сценарии Azure AD-DS настроено на использование IP-адресов 10.0.0.4 и 10.0.0.5, устанавливая тот же IP-адрес в виртуальной сети AADDS, как показано на рисунке ниже.

    ![диалоговое окно "Просмотр настраиваемых DNS-серверов"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Защита трафика LDAP

Протокол LDAP используется для чтения и записи в Active Directory. Трафик LDAP можно сделать конфиденциальным и безопасным с помощью технологии SSL (SSL) и протокола TLS. Вы можете включить LDAP через SSL (LDAPs), установив правильно отформатированный сертификат.

Дополнительные сведения о защищенном протоколе LDAP см. в статье [Настройка защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

В этом разделе вы создадите самозаверяющий сертификат, Скачайте сертификат и настроите защищенный протокол LDAP (LDAPs) для управляемого домена **хдифабрикам** Azure AD DS.

Следующий скрипт создает сертификат для хдифабрикам. Сертификат сохраняется по пути "LocalMachine".

> [!Note] 
> Для формирования запроса SSL-сертификата можно использовать любую \#служебную программу или приложение, которое создает допустимый запрос PKCS 10.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Убедитесь, что сертификат установлен в личном хранилище\'компьютера. Затем сделайте следующее:

1. Запустите консоль управления (MMC).
1. Добавьте оснастку "сертификаты", которая управляет сертификатами на локальном компьютере.
1. Разверните узел **Сертификаты (локальный компьютер)** , затем узел **Личные**и разверните узел **Сертификаты**. В личном хранилище должен присутствовать новый сертификат. Этот сертификат выдается полному имени узла.

    ![Проверка создания локального сертификата](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. На правой панели щелкните правой кнопкой мыши сертификат, созданный на предыдущем шаге, наведите указатель на пункт **все задачи**и выберите пункт **Экспорт**.

1. На странице **Экспорт закрытого ключа** нажмите кнопку **Да, экспортируйте закрытый ключ**. Закрытый ключ необходим для чтения зашифрованных сообщений с компьютера, на котором будет импортирован ключ.

    ![Мастер сертификатов, страница экспорта закрытого ключа](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. На странице **Формат экспортируемого файла**оставьте параметры по умолчанию и нажмите кнопку **Далее**. 
1. На странице **пароль** введите пароль для закрытого ключа, выберите **TripleDES-SHA1** для **шифрования** и нажмите кнопку **Далее**.
1. На странице **файл для экспорта** введите путь и имя экспортированного файла сертификата, а затем нажмите кнопку **Далее**.
1. Имя файла должно быть расширением PFX. Этот файл настраивается в портал Azure для установления безопасного подключения.
1. Включите защищенный протокол LDAP (LDAPs) для управляемого домена доменных служб Azure AD.
    1. Выберите домен **HDIFabrikam.com** из портал Azure.
    1. В разделе **Управление**щелкните **защищенный протокол LDAP** .
    1. На экране **защищенный протокол LDAP** щелкните **включить** в разделе **защищенный протокол LDAP**.
    1. Найдите PFX-файл сертификата, экспортированный на компьютере.
    1. Введите пароль сертификата.

    ![Azure AD — DS, включение защищенного протокола LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Теперь, когда вы включили защищенный протокол LDAP, убедитесь, что он доступен, включив порт 636.
    1. Щелкните группу безопасности сети **AADDS-HDIFabrikam.com-NSG** в группе ресурсов **хдифабрикам-CentralUS** .
    1. В разделе **Параметры** щелкните **правила** > безопасности для входящего трафика**Добавить**.
    1. На экране **Добавление правила безопасности для входящего трафика** введите следующие свойства и нажмите кнопку **Добавить**.

        | Свойство | Значение |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name | Port_LDAP_636 |

    ![Диалоговое окно добавления правила безопасности для входящего трафика](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`— назначенное пользователем управляемое удостоверение, роль участника доменных служб HDInsight включает управляемое удостоверение, которое позволит этому удостоверению читать, создавать, изменять и удалять операции доменных служб.

    ![Создание управляемого удостоверения, назначаемого пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Создание кластера HDInsight с поддержкой Корпоративный пакет безопасности

Для этого шага требуются следующие предварительные требования:

1. Создайте новую группу `HDIFabrikam-WestUS` ресурсов в расположении `West US`.
1. Создайте виртуальную сеть, на которой будет размещаться кластер HDInsight с поддержкой ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Создайте одноранговую связь между виртуальной сетью, в которой размещается AADDS (`HDIFabrikam-AADDSVNET`), и виртуальной сетью, в которой будет размещен кластер HDInsight с поддержкой ESP (`HDIFabrikam-HDIVNet`). Используйте следующий код PowerShell для пиринга между этими двумя виртуальными сетями.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Создайте новую учетную запись Azure Data Lake Storage 2-го поколения **Hdigen2store**, которая настроена с помощью управляемого пользователем удостоверения **хдифабрикамманажедидентити**. Дополнительные сведения о создании учетных записей Data Lake Storage 2-го поколения, включенных в управляемые пользователем удостоверения, см. в статье [использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Настройте пользовательский DNS-сервер в виртуальной сети **хдифабрикам-ааддсвнет** .
    1. Перейдите в раздел портал Azure > **группы** > ресурсов**онпремадврг** > **DNS-серверы** **хдифабрикам ааддсвнет** > .
    1. Выберите **пользовательские** и введите `10.0.0.4` и `10.0.0.5`.
    1. Нажмите кнопку **Сохранить**.

        ![Сохранить пользовательские параметры DNS для виртуальной сети](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Создайте новый кластер HDInsight Spark с поддержкой ESP.
    1. Щелкните **Настраиваемый (размер, параметры, приложения)** .
    2. Введите нужные сведения в разделе 1 **основы**. Убедитесь, что **тип кластера** — **Spark 2,3 (HDi 3,6)** , а **Группа ресурсов** — **хдифабрикам-CentralUS** .

    1. В разделе 2 **безопасность и сеть**выполните следующие действия.
        1. Щелкните **включено** в разделе **Корпоративный пакет безопасности**.
        1. Щелкните **пользователь администратора кластера** и выберите учетную запись **хдиадмин** , созданную ранее в качестве локального пользователя с правами администратора. Нажмите кнопку **Выбрать**.

        1. Щелкните **Группа доступа к кластеру** , а затем выберите **хдиусерграуп**. Любой пользователь, добавляемый в эту группу в будущем, сможет получить доступ к кластерам HDInsight.

            ![Выбор группы доступа к кластеру Хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Выполните другие действия в конфигурации кластера и проверьте сведения в **сводке по кластеру**. Нажмите кнопку **Создать**.

1. Войдите в пользовательский интерфейс Ambari для созданного кластера `https://CLUSTERNAME.azurehdinsight.net` с помощью имени `hdiadmin@hdifabrikam.com` пользователя и пароля администратора.

    ![Окно входа в пользовательский интерфейс Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. На панели мониторинга кластера щелкните **роли** .
1. На странице **роли** введите группу **хдиусерграуп** , чтобы назначить ее роли **администратора кластера** в разделе **назначение ролей**.

    ![Назначение роли администратора кластера хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Откройте SSH-клиент и войдите в кластер с помощью **hdiuser** , созданного ранее в локальной Active Directory.

    ![Вход в кластер с клиентом SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Если вы можете войти с помощью этой учетной записи, вы правильно настроили кластер ESP для синхронизации с локальной службой Active Directory.

## <a name="next-steps"></a>Следующие шаги

* [Общие сведения об обеспечении безопасности Apache Hadoop с помощью Корпоративного пакета безопасности](hdinsight-security-overview.md)
