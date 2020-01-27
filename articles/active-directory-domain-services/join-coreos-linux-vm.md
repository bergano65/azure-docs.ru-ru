---
title: Присоединение виртуальной машины CoreOS к доменным службам Azure AD | Документация Майкрософт
description: Узнайте, как настроить виртуальную машину CoreOS и присоединить ее к управляемому домену доменных служб Azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: ddf6c9238cabedfbdeeb8056864072edc543c342
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712614"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Присоединение виртуальной машины CoreOS к управляемому домену доменных служб Azure AD

Чтобы разрешить пользователям входить на виртуальные машины в Azure с помощью одного набора учетных данных, можно присоединить виртуальные машины к домену Azure Active Directory доменных служб (AD DS). При присоединении виртуальной машины к управляемому домену AD DS Azure учетные записи пользователей и учетные данные из домена можно использовать для входа и управления серверами. Также применяются членства группы из управляемого домена Azure AD DS, позволяющие управлять доступом к файлам и службам на виртуальной машине.

В этой статье показано, как присоединить виртуальную машину CoreOS к управляемому домену AD DS Azure.

## <a name="prerequisites"></a>Технические условия

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, входящая в группу *администраторов Azure AD DC* в клиенте Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Создание виртуальной машины CoreOS Linux и подключение к ней

Если у вас есть существующая виртуальная машина CoreOS Linux в Azure, подключитесь к ней с помощью SSH, а затем перейдите к следующему шагу, чтобы [приступить к настройке виртуальной машины](#configure-the-hosts-file).

Если необходимо создать ВИРТУАЛЬную машину CoreOS Linux или создать тестовую виртуальную машину для использования с этой статьей, можно использовать один из следующих методов.

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

При создании виртуальной машины Обратите внимание на параметры виртуальной сети, чтобы убедиться, что виртуальная машина может обмениваться данными с управляемым доменом AD DS Azure:

* Разверните виртуальную машину в той же или в одноранговой виртуальной сети, в которой включены доменные службы Azure AD.
* Разверните виртуальную машину в другой подсети, чем экземпляр доменных служб Azure AD.

После развертывания виртуальной машины выполните действия по подключению к виртуальной машине по протоколу SSH.

## <a name="configure-the-hosts-file"></a>Настройка файла hosts

Чтобы убедиться, что имя узла виртуальной машины правильно настроено для управляемого домена, измените файл */etc/hosts* и задайте имя узла:

```console
sudo vi /etc/hosts
```

В файле *hosts* обновите адрес *localhost* . Рассмотрим следующий пример:

* *aadds.contoso.com* — это доменное DNS-имя управляемого домена AD DS Azure.
* *CoreOS* — это имя узла виртуальной машины CoreOS, присоединяемой к управляемому домену.

Обновите эти имена собственными значениями:

```console
127.0.0.1 coreos coreos.aadds.contoso.com
```

По завершении сохраните и закройте файл *hosts* с помощью команды `:wq` редактора.

## <a name="configure-the-sssd-service"></a>Настройка службы SSSD

Обновите конфигурацию SSSD */ЕТК/сссд/сссд.конф* .

```console
sudo vi /etc/sssd/sssd.conf
```

Укажите собственное управляемое доменное имя AD DS Azure для следующих параметров:

* *домены* в верхнем регистре
* *[домен/AADDS]* , где AADDS имеет все ПРОПИСные буквы
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *KRB5_REALM* все ПРОПИСные буквы

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDS.CONTOSO.COM

[domain/AADDS.CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aadds.contoso.com
ldap_search_base = dc=aadds.contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aadds.contoso.com
krb5_realm = AADDS.CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Присоединение виртуальной машины к управляемому домену

После обновления файла конфигурации SSSD присоедините виртуальную машину к управляемому домену.

1. Сначала используйте команду `adcli info`, чтобы убедиться, что вы видите сведения об управляемом домене AD DS Azure. В следующем примере показано получение сведений для домена *AADDS. CONTOSO.COM*. Укажите собственное имя управляемого домена AD DS Azure в верхнем регистре:

    ```console
    sudo adcli info AADDS.CONTOSO.COM
    ```

   Если команде `adcli info` не удается найти управляемый домен AD DS Azure, ознакомьтесь со следующими действиями по устранению неполадок.

    * Убедитесь, что домен доступен с виртуальной машины. Попробуйте `ping aadds.contoso.com`, чтобы проверить, возвращен ли положительный ответ.
    * Убедитесь, что виртуальная машина развернута в том же или в одноранговой виртуальной сети, в которой доступен управляемый домен Azure AD DS.
    * Убедитесь, что параметры DNS-сервера для виртуальной сети были обновлены, чтобы они указывали на контроллеры домена управляемого домена AD DS Azure.

1. Теперь присоедините виртуальную машину к управляемому домену Azure AD DS с помощью команды `adcli join`. Укажите пользователя, который принадлежит к группе *администраторов контроллера домена AAD* . При необходимости [добавьте учетную запись пользователя в группу в Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Опять же, имя управляемого домена AD DS Azure необходимо вводить в верхнем регистре. В следующем примере учетная запись с именем `contosoadmin@aadds.contoso.com` используется для инициализации Kerberos. Введите собственную учетную запись пользователя, которая является членом группы *администраторов контроллера домена AAD* .

    ```console
    sudo adcli join -D AADDS.CONTOSO.COM -U contosoadmin@AADDS.CONTOSO.COM -K /etc/krb5.keytab -H coreos.aadds.contoso.com -N coreos
    ```

    Команда `adcli join` не возвращает никаких сведений о том, что виртуальная машина успешно присоединена к управляемому домену Azure AD DS.

1. Чтобы применить конфигурацию присоединение к домену, запустите службу SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Вход в виртуальную машину с помощью учетной записи домена

Чтобы убедиться, что виртуальная машина успешно присоединена к управляемому домену Azure AD DS, запустите новое SSH-подключение, используя учетную запись пользователя домена. Убедитесь, что был создан корневой каталог и применяется членство в группе из домена.

1. Создайте новое SSH-подключение из консоли. Используйте учетную запись домена, принадлежащую к управляемому домену, с помощью команды `ssh -l`, например `contosoadmin@aadds.contoso.com`, а затем введите адрес виртуальной машины, например *CoreOS.aadds.contoso.com*. При использовании Azure Cloud Shell Используйте общедоступный IP-адрес виртуальной машины, а не внутреннее DNS-имя.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com coreos.aadds.contoso.com
    ```

1. Теперь убедитесь, что членство в группе разрешается правильно:

    ```console
    id
    ```

    Вы должны увидеть членство в группе из управляемого домена Azure AD DS.

## <a name="next-steps"></a>Дальнейшие действия

Если при подключении виртуальной машины к управляемому домену AD DS Azure или при входе с помощью учетной записи домена возникли проблемы, см. раздел [Устранение неполадок при присоединении к домену](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
