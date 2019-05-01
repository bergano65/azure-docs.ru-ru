---
title: Архитектура Azure HDInsight с корпоративным пакетом безопасности
description: Дополнительные сведения о планировании безопасности HDInsight с корпоративным пакетом безопасности.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f81aa1e493014f192dc11ca698e9793075ac26ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725612"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Корпоративный пакет безопасности для HDInsight

Стандартный кластер Azure HDInsight рассчитан на одного пользователя. Он подходит для большинства организаций с небольшими отделами по работе с приложениями, создающими объемные рабочие нагрузки данных. Для каждого пользователя по запросу может быть создан выделенный кластер, который может быть удален, когда перестанет быть нужен. 

Многие организации перешли на модель, в которой команды ИТ-специалистов управляют кластерами, а команды из нескольких приложений совместно используют кластеры. Таким крупным предприятиям требуется многопользовательский доступ к кластерам в Azure HDInsight.

HDInsight использует наиболее популярный поставщик удостоверений — управляемую службу Active Directory. Благодаря интеграции HDInsight с [доменными службами Azure Active Directory (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md) доступ к кластерам можно получить с помощью учетных данных домена. 

Виртуальные машины в HDInsight присоединены к указанному домену. Все службы, запущенные в HDInsight (Apache Ambari, сервер Apache Hive, Apache Ranger, сервер Apache Spark Thrift и т. п.), согласованно работают для пользователя, прошедшего поддержку подлинности. Администраторы могут настраивать строгие политики авторизации на основе Apache Ranger, чтобы применять управление доступом на основе ролей для ресурсов в кластере.

## <a name="integrate-hdinsight-with-active-directory"></a>Интеграция HDInsight с Active Directory

Apache Hadoop с открытым кодом полагается на протокол Kerberos для аутентификации и обеспечения безопасности. Таким образом, узлы кластера HDInsight с корпоративным пакетом безопасности присоединены к домену под управлением Azure AD DS. Для включенных в кластер компонентов Hadoop настраиваются параметры безопасности Kerberos. 

Указанные ниже действия создаются автоматически.

- Субъект-служба для каждого компонента Hadoop.
- Соответствующий субъект компьютера для каждого компьютера, присоединенного к домену.
- Подразделение (OU) для каждого кластера для хранения этих субъектов службы и компьютера.

Кратко перечислим, что вам нужно создать в сетевом окружении:

- Домен Active Directory (под управлением Azure AD DS).
- В Azure AD DS должен быть включен протокол LDAPS.
- Соответствующее сетевое подключение виртуальной сети HDInsight к виртуальной сети Azure AD DS, если вы выбрали для них отдельные виртуальные сети. Виртуальная машина в виртуальной сети HDInsight должна иметь возможность напрямую обращаться к Azure AD DS через пиринг виртуальных сетей. Если HDInsight и Azure AD DS развернуты в одной виртуальной сети, подключение предоставляется автоматически и никаких дальнейших действий не требуется.

## <a name="set-up-different-domain-controllers"></a>Настройка разных контроллеров домена
HDInsight в настоящее время поддерживает только Azure AD DS в качестве основного контроллера домена, используемого кластером для передачи данных по протоколу Kerberos. Но возможны и другие сложные конфигурации Active Directory, при условии, что они обеспечивают доступ к HDInsight для Azure AD DS.

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) предоставляет управляемый домен, который полностью совместим с Windows Server Active Directory. Корпорация Майкрософт управляет доменом, применяет к нему исправления и осуществляет его мониторинг в конфигурации высокой доступности. Вы можете развернуть свой кластер, не беспокоясь о поддержке контроллеров домена. 

Пользователи, группы и пароли синхронизируются из Azure AD. Однонаправленная синхронизация из экземпляра Azure AD в Azure AD DS позволяет пользователям входить в кластер, используя те же учетные данные организации. 

Дополнительные сведения см. в разделе [Настройка кластеров HDInsight с ESP с помощью доменных служб Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Локальная служба Active Directory или служба Active Directory на виртуальных машинах IaaS

Если у вас используется локальный экземпляр Active Directory или более сложная конфигурация Active Directory для домена, то вы можете синхронизировать их удостоверения в Azure AD с помощью Azure AD Connect. Затем вы можете включить Azure AD DS на этом клиенте Active Directory. 

Так как в протоколе Kerberos используются хэши паролей, необходимо будет [включить синхронизацию хэша пароля в Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Если вы используете федерацию на основе службы федерации Active Directory (AD FS), необходимо включить синхронизацию хэша паролей. (Рекомендуемые настройки см. в [этом видео](https://youtu.be/qQruArbu2Ew).) Синхронизация хэша паролей помогает при аварийном восстановлении в случае сбоя инфраструктуры AD FS, а также обеспечивает защиту утечки учетных данных. Дополнительные сведения см. в статье [Реализация синхронизации хэшированных паролей в службе синхронизации Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Использование только локальной службы Active Directory или Active Directory на виртуальных машинах IaaS, без Azure AD и Azure AD DS, не поддерживается для кластеров HDInsight с ESP.

Если используется федерация и хэши паролей синхронизируются правильно, но вы получаете сбой при аутентификации, проверьте, включена ли проверка пароля в облаке субъекта-службы PowerShell. Если нет, вы должны установить [Политику обнаружения домашней области (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) для вашего клиента Azure AD. Чтобы проверить и установить политику HRD, выполните следующие действия.

1. Установите модуль Azure AD PowerShell.

   ```
   Install-Module AzureAD
   ```

2. Введите `Connect-AzureAD` с помощью учетных данных глобального администратора (администратора клиента).

3. Проверьте, была ли уже создана субъект-служба Microsoft Azure PowerShell.

   ```
   $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Если нет (т. е. если `($powershellSPN -eq $null)`), то создайте субъект-службу.

   ```
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Создайте и примените политику к этому субъекту-службе.

   ```
   $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

   Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory](apache-domain-joined-configure-using-azure-adds.md)
* [Настройка политик Apache Hive в HDInsight с Корпоративным пакетом безопасности](apache-domain-joined-run-hive.md)
* [Управление кластерами HDInsight с помощью корпоративного пакета безопасности](apache-domain-joined-manage.md) 
