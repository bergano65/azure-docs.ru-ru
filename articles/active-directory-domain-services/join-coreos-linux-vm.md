---
title: Присоединитесь к службе домена CoreOS VM в службу домена Azure AD (ru) Документы Майкрософт
description: Узнайте, как настроить и присоединиться к виртуальной машине CoreOS к домену Azure AD Domain Services, управляемому доменом.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655197"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Присоединиться к виртуальной машине CoreOS к домену Azure AD Domain

Чтобы позволить пользователям войти в виртуальные машины (Виртуальные технологии) в Azure, используя единый набор учетных данных, можно присоединиться к виртуальным всасданным службам функционального каталога Azure (AD DS), управляемым доменом. При присоединении к VM к домену Azure AD DS, учетные записи пользователей и учетные данные домена могут использоваться для ввоза и управления серверами. Членство в группе из домена Azure AD DS также применяется, чтобы позволить вам контролировать доступ к файлам или службам на VM.

В этой статье показано, как присоединиться к CoreOS VM к домену Azure AD DS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, войдя часть домена Azure AD DS.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Создание и подключение к CoreOS Linux VM

Если у вас есть существующий CoreOS Linux VM в Azure, подключитесь к нему с помощью SSH, а затем перейдите на следующий шаг, чтобы [начать настройку VM.](#configure-the-hosts-file)

Если вам нужно создать CoreOS Linux VM, или хотите создать тест VM для использования в этой статье, вы можете использовать один из следующих методов:

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

При создании VM обратите внимание на настройки виртуальной сети, чтобы убедиться, что VM может общаться с доменом Azure AD DS:

* Развертывание VM в ту же или заглянувую виртуальную сеть, в которой вы включили службы домена Azure AD.
* Развертывание VM в другую подсеть, чем экземпляр службы домена Azure AD.

После развертывания VM выполните последующие действия по подключению к VM с помощью SSH.

## <a name="configure-the-hosts-file"></a>Настройка файла hosts

Чтобы убедиться, что имя хоста VM правильно настроено для управляемого домена, отредактьте файл */etc/hosts* и установите имя хоста:

```console
sudo vi /etc/hosts
```

В файле *хостов* обновите адрес *локального хозяина.* Рассмотрим следующий пример:

* *aaddscontoso.com* — доменное имя DNS вашего домена Azure AD DS.
* *coreos* — это хост-имя вашего CoreOS VM, к которому вы присоединяетесь к управляемому домену.

Обновление этих имен с вашими собственными значениями:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

При этом сохраняй и выходизьте файл *хоста,* `:wq` используя команду редактора.

## <a name="configure-the-sssd-service"></a>Настройка службы SSSD

Обновление */etc/sssd/sssd.conf* SSSD конфигурации.

```console
sudo vi /etc/sssd/sssd.conf
```

Укажите свой собственный доменное имя Azure AD DS по следующим параметрам:

* *домены* в ALL UPPER CASE
* *(домен/AADDS),* где AADDS находится в ВСЕХ UPPER CASE
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* в ALL UPPER CASE

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Присоединиться к VM к управляемому домену

С обновлением файла конфигурации SSSD теперь присоединяйтесь к виртуальной машине к управляемому домену.

1. Во-первых, `adcli info` используйте команду, чтобы проверить, что вы можете видеть информацию об домене Azure AD DS. Следующий пример получает информацию для *домена AADDSCONTOSO.COM*. Укажите свой собственный Azure AD DS управляемых доменное имя в ВСЕХ UPPERCASE:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Если `adcli info` команда не может найти свой домен СУ AD СAzура, просмотрите следующие шаги устранения неполадок:

    * Убедитесь, что домен довисот от VM. Попробуйте `ping aaddscontoso.com` увидеть, если положительный ответ возвращается.
    * Убедитесь, что VM развернут в той же виртуальной сети, в которой доступен домен Azure AD DS.
    * Подтвердите, что настройки DNS сервера для виртуальной сети были обновлены, чтобы указать на контроллеры домена в домене Azure AD DS.

1. Теперь присоединяйтесь к VM в управляемый `adcli join` домен Azure AD DS с помощью команды. Укажите пользователя, который является частью домена Azure AD DS. При необходимости [добавьте учетную запись пользователя в группу в Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Опять же, доменное имя Azure AD DS, управляемое DS, должно быть введено в ALL UPPERCASE. В следующем примере названная `contosoadmin@aaddscontoso.com` учетная запись используется для инициализации Kerberos. Введите свою собственную учетную запись пользователя, которая является частью домена Azure AD DS.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Команда `adcli join` не возвращает информацию, когда VM успешно присоединился к домену Azure AD DS.

1. Чтобы применить конфигурацию соединения домена, запустите службу SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Войти на VM с помощью учетной записи домена

Чтобы убедиться, что VM был успешно подключен к домену Azure AD DS, запустите новое соединение SSH с помощью учетной записи пользователя домена. Подтвердите, что домашний каталог был создан, и что членство в группе из домена применяется.

1. Создайте новое соединение SSH с консоли. Используйте учетную запись домена, которая `ssh -l` принадлежит управляемому домену, используя команду, `contosoadmin@aaddscontoso.com` например, а затем введите адрес вашего VM, *например, coreos.aaddscontoso.com.* Если вы используете Лазурную облачную оболочку, используйте общедоступный IP-адрес VM, а не внутреннее имя DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Теперь убедитесь, что членство в группе решается правильно:

    ```console
    id
    ```

    Вы должны видеть членство в группе из домена Azure AD DS.

## <a name="next-steps"></a>Дальнейшие действия

Если у вас возникли проблемы с подключением VM к домену Azure [Troubleshooting domain join issues](join-windows-vm.md#troubleshoot-domain-join-issues)AD DS или вовремя с помощью учетной записи домена, см.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
