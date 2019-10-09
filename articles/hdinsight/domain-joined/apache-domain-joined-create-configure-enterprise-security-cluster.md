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
ms.openlocfilehash: aed656c65fc70946f7d91cb4354e1c081954e68c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030396"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Создание и настройка кластеров Корпоративный пакет безопасности в Azure HDInsight

Корпоративный пакет безопасности (ESP) для Azure HDInsight предоставляет доступ к аутентификации на основе Active Directory, многопользовательской поддержке и управлению доступом на основе ролей для кластеров Apache Hadoop в Azure. Кластеры HDInsight ESP позволяют организациям безопасно обрабатывать конфиденциальные данные в соответствии с ограниченными корпоративными политиками безопасности.

В этом руководство показано, как создать кластер Azure HDInsight с поддержкой ESP. Здесь также показано, как создать виртуальную машину IaaS Windows, на которой включены Active Directory и служба доменных имен (DNS). Используйте это руководством для настройки необходимых ресурсов, чтобы разрешить локальным пользователям входить в кластер HDInsight с поддержкой ESP.

Созданный вами сервер будет использоваться в качестве замены для *реальной* локальной среды. Вы будете использовать его для настройки и настройки. Позже вы повторите действия в своей среде. 

Это также поможет вам создать гибридную среду удостоверений с помощью синхронизации хэшей паролей с Azure Active Directory (Azure AD). В этом руководством [используется ESP в HDInsight](apache-domain-joined-architecture.md).

Перед использованием этого процесса в собственной среде выполните следующие действия.
* Настройте Active Directory и DNS.
* Включите Azure AD.
* Синхронизация локальных учетных записей пользователей с Azure AD.

![Схема архитектуры Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Создание локальной среды

В этом разделе вы будете использовать шаблон развертывания быстрого запуска Azure для создания новых виртуальных машин, настройки DNS и добавления нового Active Directory леса.

1. Перейдите к шаблону быстрого развертывания, чтобы [создать виртуальную машину Azure с новым Active Directory лесом](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Выберите **развертывание в Azure**.
1. Войдите в свою подписку Azure.
1. На странице **Создание виртуальной машины Azure с помощью нового леса AD** выполните следующие действия.
    1. В раскрывающемся списке **Подписка** выберите подписку, в которой требуется развернуть ресурсы.
    1. Рядом с **группой ресурсов**выберите **создать**и введите имя *онпремадврг*.
    1. В остальных полях шаблона введите следующие сведения:

        * **Расположение.** Центральный регион США
        * **Имя администратора**. хдифабрикамадмин
        * **Пароль администратора**. @NO__T 0YOUR_PASSWORD >
        * **Доменное имя**. HDIFabrikam.com
        * **Префикс DNS**: хдифабрикам

        ![Шаблон для создания виртуальной машины Azure с новым лесом Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Щелкните **Приобрести**.
    1. Отслеживайте развертывание и дождитесь его завершения.
    1. Убедитесь, что ресурсы созданы в правильной группе ресурсов **онпремадврг**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Настройка пользователей и групп для доступа к кластеру

В этом разделе вы создадите пользователей, которые будут иметь доступ к кластеру HDInsight, в конце этого руководством.

1. Подключитесь к контроллеру домена с помощью удаленный рабочий стол.
    1. Если вы использовали шаблон, упомянутый в начале, то контроллер домена — это виртуальная машина с именем **адвм** в группе ресурсов **онпремадврг** .
    1. В портал Azure выберите **группы ресурсов** > **онпремадврг** > **адвм** > **Connect**.
    1. Выберите вкладку **rdp** > **скачать RDP-файл**.
    1. Сохраните файл на компьютере и откройте его.
    1. При появлении запроса на ввод учетных данных используйте *хдифабрикам\хдифабрикамадмин* в качестве имени пользователя. Затем введите пароль, выбранный для учетной записи администратора.

1. Когда сеанс удаленный рабочий стол откроется на виртуальной машине контроллера домена, на панели мониторинга **Диспетчер сервера** откройте **Active Directory пользователи и компьютеры**. В правом верхнем углу выберите **инструменты** > **Active Directory пользователи и компьютеры**.

    ![На панели мониторинга диспетчер сервера откройте управление Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Создайте двух новых пользователей: **Хдиадмин** и **HDIUser**. Эти два пользователя будут входить в кластеры HDInsight.

    1. На странице **Active Directory пользователи и компьютеры** выберите **действие** > **создать** > **пользователя**.

        ![Создание нового Active Directory пользователя](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. На странице **новый объект — пользователь** введите в поле **имя входа пользователя** *HDIUser*. Затем нажмите кнопку **Далее**.

        ![Создание первого объекта пользователя "Администратор"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Во всплывающем окне введите пароль для новой учетной записи. Выберите **срок действия пароля никогда не истекает** > **ОК**.
    1. Нажмите кнопку **Готово** , чтобы создать новую учетную запись.
    1. Создайте другого пользователя с именем *хдиадмин*.

        ![Создание второго объекта пользователя "Администратор"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. На странице **Active Directory пользователи и компьютеры** выберите **действие** > **создать** **группу** > . Добавьте группу с именем *хдиусерграуп*.

    ![Создание новой группы Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Создать новый объект](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Добавьте **HDIUser** в **хдиусерграуп** в качестве члена группы.

    1. Щелкните правой кнопкой мыши **хдиусерграуп** и выберите пункт **свойства**.
    1. На вкладке **Члены** выберите **Добавить**.
    1. В поле **Введите имена объектов для выбора** введите *HDIUser*. Нажмите кнопку **ОК**.
    1. Повторите предыдущие шаги для учетной записи **хдиадмин** .

        ![Добавление члена HDIUser в группу Хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Теперь вы создали среду Active Directory. Вы добавили двух пользователей и группу пользователей, которые имеют доступ к кластеру HDInsight.

Пользователи будут синхронизированы с Azure AD.

### <a name="create-an-azure-ad-directory"></a>Создание каталога Azure AD

1. Войдите на портал Azure.
1. Выберите **создать ресурс** и тип *Каталог*. Выберите **Azure Active Directory** > **создать**.
1. В разделе **название организации**введите *хдифабрикам*.
1. В поле **первоначальное доменное имя**введите *хдифабрикамаутлук*.
1. Нажмите кнопку **Создать**.

    ![Создание каталога Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. В левой части портал Azure выберите **Azure Active Directory**.
1. При необходимости выберите **параметр переключить каталог** , чтобы перейти в новый каталог **хдифабрикамаутлук** .
1. В разделе **Управление**выберите **пользовательские доменные имена** > **Добавить личный домен**.
1. В поле **имя личного домена**введите *HDIFabrikam.com* и выберите **Добавить домен**.

![Создание пользовательского домена](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Настройка клиента Azure AD

Теперь вы настроите клиент Azure AD, чтобы вы могли синхронизировать пользователей и группы из локального экземпляра Active Directory с облаком.

1. Создайте Active Directory администратора клиента.
    1. Войдите в портал Azure и выберите свой клиент Azure AD **хдифабрикам**.
    1. В разделе **Управление**выберите **Пользователи** > **Новый пользователь**.
    1. Введите следующие сведения для нового пользователя:
        * **Имя**: фабрикамазуреадмин
        * **Имя пользователя**: fabrikamazureadmin@hdifabrikam.com
        * **Пароль**. Безопасный пароль по своему усмотрению

    1. В разделе **группы** найдите **администраторов контроллера домена AAD** и нажмите кнопку **выбрать**.

        ![Диалоговое окно "группы Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Откройте раздел **роль каталога** и справа выберите **глобальный администратор** > **ОК**.

        ![Диалоговое окно "роль Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Введите пароль для пользователя. Щелкните **Создать**.

1. Если вы хотите изменить пароль только что созданного пользователя \< @ no__t-1 >, используйте удостоверение для входа в портал Azure. Затем вам будет предложено изменить пароль.

## <a name="sync-on-premises-users-to-azure-ad"></a>Синхронизация локальных пользователей с Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Скачивание и установка Azure AD Connect

1. [Скачать Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)

1. Установите Azure AD Connect на контроллере домена.

    1. Откройте загруженный исполняемый файл и примите условия лицензии. Выберите **Продолжить**.

        ![Страница "Добро пожаловать в Azure AD Connect"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Установите флажок **использовать Экспресс параметры** и завершите установку.

        ![Параметры Azure AD Connect Express](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Настройка синхронизации с локальным контроллером домена

1. На странице **Подключение к Azure AD** введите имя пользователя и пароль глобального администратора для Azure AD. Используйте имя пользователя `fabrikamazureadmin@hdifabrikam.com`, созданное при настройке клиента Active Directory. Затем нажмите кнопку **Далее**. 

    ![Страница "подключение к Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. На странице **Подключение к домен Active Directory службам** введите имя пользователя и пароль для учетной записи администратора предприятия. Используйте имя пользователя `HDIFabrikam\HDIFabrikamAdmin` и пароль, созданный ранее. Затем нажмите кнопку **Далее**. 

   ![Страница "подключение к Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. На странице **Настройка входа в Azure AD** нажмите кнопку **Далее**.
   ![The "Конфигурация входа в Azure AD", страница @ no__t-1

1. На странице **все готово для настройки** нажмите кнопку **установить**.

   ![Страница "все готово для настройки"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. На странице **Конфигурация завершена** выберите **Выйти**.
   ![The "Конфигурация завершена", страница @ no__t-1

1. После завершения синхронизации убедитесь, что пользователи, созданные в каталоге IaaS, синхронизированы с Azure AD.
   1. Войдите на портал Azure.
   1. Выберите **Azure Active Directory** > **хдифабрикам** **пользователей** > .

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте управляемое пользователем удостоверение, которое можно использовать для настройки доменных служб Azure AD (AD DS Azure). Дополнительные сведения см. [в разделе Создание, перечисление, удаление или назначение роли назначенному пользователем управляемому удостоверению с помощью портал Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Войдите на портал Azure.
1. Выберите **создать ресурс** и введите *управляемое удостоверение*. Выберите **назначенное пользователем управляемое удостоверение** > **создать**.
1. В поле **имя ресурса**введите *хдифабрикамманажедидентити*.
1. Выберите свою подписку.
1. В разделе **Группа ресурсов**выберите **создать** и введите *хдифабрикам-CentralUS*.
1. В разделе **Расположение**выберите **Центральная американская**.
1. Нажмите кнопку **Создать**.

![Создание нового управляемого удостоверения, назначенного пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Включение Azure AD DS

Чтобы включить AD DS Azure, выполните следующие действия. Дополнительные сведения см. [в разделе Включение Azure AD DS с помощью портал Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Создайте виртуальную сеть для размещения AD DS Azure. Выполните следующий код PowerShell.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Войдите на портал Azure.
1. Выберите **создать ресурс**, введите *доменные службы*и выберите **доменные службы Azure AD**.
1. На странице **Основные сведения** :
    1. В разделе **имя каталога**выберите созданный каталог Azure AD. **Хдифабрикам**.
    1. В качестве **имени домена DNS**введите *HDIFabrikam.com*.
    1. Выберите свою подписку.
    1. Укажите группу ресурсов **хдифабрикам-CentralUS**. В качестве **расположения**выберите **Central US**.

        ![Основные сведения об Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. На странице **сеть** выберите сеть (**хдифабрикам-vnet**) и подсеть (**AADDS-Subnet**), созданную с помощью сценария PowerShell. Или выберите **создать** , чтобы создать виртуальную сеть прямо сейчас.

    ![Шаг "Создание виртуальной сети"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. На странице **Группа администраторов** должно появиться уведомление о том, что группа с именем **Администраторы контроллера домена AAD** уже создана для администрирования этой группы. Вы можете изменить членство в этой группе, если хотите, но в данном случае ее не нужно изменять. Нажмите кнопку **ОК**.

    ![Просмотр группы администраторов Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. На странице **Синхронизация** Включите полную синхронизацию, выбрав **все** > **ОК**.

    ![Включение синхронизации AD DS Azure](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. На странице **Сводка** проверьте сведения о AD DS Azure и нажмите кнопку **ОК**.

    ![Сводка по включению доменных служб Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

После включения AD DS Azure локальный DNS-сервер будет работать на виртуальных машинах Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Настройка виртуальной сети Azure AD DS

Выполните следующие действия, чтобы настроить виртуальную сеть Azure AD DS (**хдифабрикам-ааддсвнет**) для использования пользовательских DNS-серверов.

1. Нахождение IP-адресов пользовательских DNS-серверов. 
    1. Выберите ресурс Azure AD DS **HDIFabrikam.com** . 
    1. В разделе **Управление**выберите **свойства**. 
    1. Найдите IP-адреса в разделе **IP-адрес в виртуальной сети**.

    ![Поиск пользовательских IP-адресов DNS для Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Настройте **хдифабрикам-ааддсвнет** для использования НАСТРАИВАЕМЫХ IP-адресов 10.0.0.4 и 10.0.0.5.

    1. В разделе **Параметры**выберите **DNS-серверы**. 
    1. Выберите **Пользовательский**.
    1. В текстовом поле введите первый IP-адрес (*10.0.0.4*).
    1. Щелкните **Сохранить**.
    1. Повторите шаги, чтобы добавить другой IP-адрес (*10.0.0.5*).

В нашем сценарии мы настроили Azure AD DS для использования IP-адресов 10.0.0.4 и 10.0.0.5, задав тот же IP-адрес в виртуальной сети Azure AD DS.

![Страница "пользовательские DNS-серверы"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Защита трафика LDAP

Протокол LDAP используется для чтения и записи в Azure Active Directory. Трафик LDAP можно сделать конфиденциальным и безопасным с помощью технологии SSL (SSL) или протокола TLS. Вы можете включить LDAP через SSL (LDAPs), установив правильно отформатированный сертификат.

Дополнительные сведения о защищенном протоколе LDAP см. в статье [Настройка LDAPS для управляемого домена AD DS Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

В этом разделе вы создадите самозаверяющий сертификат, Скачайте сертификат и настроите LDAPs для управляемого домена **хдифабрикам** Azure AD DS.

Следующий скрипт создает сертификат для **хдифабрикам**. Сертификат сохраняется в пути *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Для формирования запроса SSL-сертификата можно использовать любую программу или приложение, которое создает допустимый запрос PKCS \#10 с открытым ключом.

Убедитесь, что сертификат установлен в **личном** хранилище компьютера:

1. Запустите консоль управления (MMC).
1. Добавьте оснастку " **Сертификаты** ", которая управляет сертификатами на локальном компьютере.
1. Разверните узел **Сертификаты (локальный компьютер)**  > **персональные** **Сертификаты** > . В **личном** хранилище должен присутствовать новый сертификат. Этот сертификат выдается полному имени узла.

    ![Проверка создания локального сертификата](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. В области справа щелкните созданный сертификат правой кнопкой мыши. Наведите указатель на пункт **все задачи**, а затем выберите **Экспорт**.

1. На странице **Экспорт закрытого ключа** выберите **Да, экспортировать закрытый ключ**. На компьютере, на котором будет импортирован ключ, требуется закрытый ключ для чтения зашифрованных сообщений.

    ![Страница «Экспорт закрытого ключа» мастера экспорта сертификатов](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. На странице **Формат экспортируемого файла** оставьте параметры по умолчанию, а затем нажмите кнопку **Далее**. 
1. На странице **пароль** введите пароль для закрытого ключа. В качестве **шифрования**выберите **TripleDES-SHA1**. Затем нажмите кнопку **Далее**.
1. На странице **файл для экспорта** введите путь и имя экспортированного файла сертификата, а затем нажмите кнопку **Далее**. Имя файла должно иметь расширение PFX. Этот файл настраивается в портал Azure для установления безопасного подключения.
1. Включите LDAPs для управляемого домена Azure AD DS.
    1. В портал Azure выберите домен **HDIFabrikam.com**.
    1. В разделе **Управление**выберите **защищенный протокол LDAP**.
    1. На странице **защищенный протокол LDAP** в разделе **защищенный протокол LDAP**выберите **включить**.
    1. Найдите PFX-файл сертификата, экспортированный на компьютере.
    1. Введите пароль сертификата.

    ![Включение защищенного протокола LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Теперь, когда вы включили LDAPs, убедитесь, что он доступен, включив порт 636.
    1. В группе ресурсов **хдифабрикам-CentralUS** выберите группу безопасности сети **AADDS-HDIFabrikam.com-NSG**.
    1. В разделе **Параметры**выберите **правила безопасности для входящего трафика** > **добавить**.
    1. На странице **Добавление правила безопасности для входящего трафика** введите следующие свойства и нажмите кнопку **Добавить**.

        | Свойство | Значение |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | @no__t 0Desired number > |
        | ИМЯ | Port_LDAP_636 |

    ![Диалоговое окно "Добавление правила безопасности для входящего трафика"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**Хдифабрикамманажедидентити** — назначенное пользователем управляемое удостоверение. Для роли участника доменных служб HDInsight включено управляемое удостоверение, которое позволит этому удостоверению читать, создавать, изменять и удалять операции доменных служб.

![Создание управляемого удостоверения, назначаемого пользователем](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Создание кластера HDInsight с поддержкой ESP

Для этого шага требуются следующие компоненты:

1. Создайте новую группу ресурсов *хдифабрикам-WestUS* в расположении " **Западная часть США**".
1. Создайте виртуальную сеть, в которой будет размещен кластер HDInsight с поддержкой ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Создайте одноранговую связь между виртуальной сетью, в которой размещается AD DS Azure (`HDIFabrikam-AADDSVNET`), и виртуальной сетью, в которой будет размещен кластер HDInsight с поддержкой ESP (`HDIFabrikam-HDIVNet`). Используйте следующий код PowerShell для пиринга между двумя виртуальными сетями.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Создайте новую учетную запись Azure Data Lake Storage 2-го поколения с именем **Hdigen2store**. Настройте учетную запись с управляемым пользователем удостоверением **хдифабрикамманажедидентити**. Дополнительные сведения см. в статье [использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Настройте пользовательский DNS-сервер в виртуальной сети **хдифабрикам-ааддсвнет** .
    1. Перейдите в раздел портал Azure > **группы ресурсов** > **онпремадврг** > **хдифабрикам-ааддсвнет** > **DNS-серверов**.
    1. Выберите **Custom (пользовательские** ) и введите *10.0.0.4* и *10.0.0.5*.
    1. Щелкните **Сохранить**.

        ![Сохранение пользовательских параметров DNS для виртуальной сети](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Создайте новый кластер HDInsight Spark с поддержкой ESP.
    1. Выберите **Пользовательский (размер, параметры, приложения)** .
    2. Введите сведения для **основ** (раздел 1). Убедитесь, что **тип кластера** — **Spark 2,3 (HDi 3,6)** . Убедитесь, что **Группа ресурсов** — **хдифабрикам-CentralUS**.

    1. В разделе " **безопасность и сеть** " (раздел 2) введите следующие сведения:
        * В разделе **Корпоративный пакет безопасности**выберите **включено**.
        * Выберите **пользователь администратора кластера** и выберите учетную запись **хдиадмин** , созданную в качестве локального пользователя с правами администратора. Нажмите кнопку **Выбрать**.
        * Выберите **Группа доступа к кластеру** > **хдиусерграуп**. Любой пользователь, добавляемый в эту группу в будущем, сможет получить доступ к кластерам HDInsight.

            ![Выберите группу доступа к кластеру Хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Выполните другие действия в конфигурации кластера и проверьте сведения в **сводке по кластеру**. Нажмите кнопку **Создать**.

1. Войдите в пользовательский интерфейс Ambari для созданного кластера на `https://CLUSTERNAME.azurehdinsight.net`. Используйте имя пользователя администратора `hdiadmin@hdifabrikam.com` и пароль.

    ![Окно входа в пользовательский интерфейс Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. На панели мониторинга кластера выберите **роли**.
1. На странице **роли** в разделе **назначение ролей**, рядом с ролью **Администратор кластера** введите группу *хдиусерграуп*. 

    ![Назначение роли администратора кластера хдиусерграуп](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Откройте клиент Secure Shell (SSH) и войдите в кластер. Используйте **hdiuser** , созданный в локальном экземпляре Active Directory.

    ![Вход в кластер с помощью SSH-клиента](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Если вы можете войти с помощью этой учетной записи, вы правильно настроили кластер ESP для синхронизации с локальным экземпляром Active Directory.

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с [введением в Apache Hadoop безопасность с помощью ESP](hdinsight-security-overview.md).
