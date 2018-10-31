---
title: Архитектура Azure HDInsight с корпоративным пакетом безопасности
description: Дополнительные сведения о планировании безопасности HDInsight с корпоративным пакетом безопасности.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8d344adc367eb9b93e52d9423a2ab4dda657b298
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115545"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Корпоративный пакет безопасности для HDInsight

Стандартный кластер Azure HDInsight рассчитан на одного пользователя. Он подходит для большинства организаций с небольшими отделами по работе с приложениями, создающими объемные рабочие нагрузки данных. Для каждого пользователя по запросу может быть создан выделенный кластер, который может быть удален, когда перестанет быть нужен. 

Многие организации перешли на новую модель. В ней команды ИТ-специалистов управляют кластерами, которые совместно используются несколькими командами по разработке приложений. Таким крупным предприятиям требуется многопользовательский доступ к кластерам в Azure HDInsight.

HDInsight использует наиболее популярный поставщик удостоверений — управляемую службу Active Directory. Благодаря интеграции HDInsight с [доменными службами Azure Active Directory (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md) доступ к кластерам можно получить с помощью учетных данных домена. 

Виртуальные машины в HDInsight присоединены к указанному домену. Поэтому все службы, запущенные в HDInsight (Ambari, сервер Hive, Ranger, сервер Spark Thrift и т. д.), работают прозрачно для аутентифицированного пользователя. Администраторы могут настраивать строгие политики авторизации на основе Apache Ranger, чтобы применять управление доступом на основе ролей для ресурсов в кластере.

## <a name="integrate-hdinsight-with-active-directory"></a>Интеграция HDInsight с Active Directory

Hadoop с открытым кодом использует протокол Kerberos для аутентификации и обеспечения безопасности. Таким образом, узлы кластера HDInsight с корпоративным пакетом безопасности присоединены к домену под управлением Azure AD DS. Для включенных в кластер компонентов Hadoop настраиваются параметры безопасности Kerberos. 

Указанные ниже действия создаются автоматически.
- Субъект-служба для каждого компонента Hadoop. 
- Соответствующий субъект компьютера для каждого компьютера, присоединенного к домену.
- Подразделение (OU) для каждого кластера для хранения этих субъектов службы и компьютера. 

Кратко перечислим, что вам нужно создать в сетевом окружении:

- Домен Active Directory (под управлением Azure AD DS).
- В Azure AD DS должен быть включен протокол LDAPS.
- Соответствующее сетевое подключение виртуальной сети HDInsight к виртуальной сети Azure AD DS, если вы выбрали для них отдельные виртуальные сети. Виртуальная машина в виртуальной сети HDInsight должна иметь возможность напрямую обращаться к Azure AD DS через пиринг виртуальных сетей. Если HDInsight и Azure AD DS развернуты в одной виртуальной сети, подключение предоставляется автоматически и никаких дальнейших действий не требуется.

## <a name="set-up-different-domain-controllers"></a>Настройка разных контроллеров домена
HDInsight в настоящее время поддерживает только Azure AD DS в качестве основного контроллера домена, используемого кластером для передачи данных по протоколу Kerberos. Но возможны и другие сложные конфигурации Active Directory, при условии, что они обеспечивают доступ к HDInsight для Azure AD DS.

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) предоставляет управляемый домен, который полностью совместим с Windows Server Active Directory. Корпорация Майкрософт управляет доменом, применяет к нему исправления и осуществляет его мониторинг в конфигурации высокой доступности. Вы можете развернуть свой кластер, не беспокоясь о поддержке контроллеров домена. 

Пользователи, группы и пароли синхронизируются из Azure Active Directory (Azure AD). Однонаправленная синхронизация из экземпляра Azure AD в Azure AD DS позволяет пользователям входить в кластер, используя те же учетные данные организации. 

Дополнительные сведения см. в разделе [Настройка кластеров HDInsight с ESP с помощью доменных служб Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Локальная служба Active Directory или служба Active Directory на виртуальных машинах IaaS

Если у вас используется локальный экземпляр Active Directory или более сложная конфигурация Active Directory для домена, то вы можете синхронизировать их удостоверения в Azure AD с помощью Azure AD Connect. Затем вы можете включить Azure AD DS на этом клиенте Active Directory. 

Так как в протоколе Kerberos используются хэши паролей, необходимо будет [включить синхронизацию хэша пароля в Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Если вы используете федерацию на основе служб федерации Active Directory (AD FS), то у вас есть возможность настроить синхронизацию паролей для дополнительной защиты на случай сбоя в инфраструктуре AD FS. Дополнительные сведения см. в статье [Реализация синхронизации хэшированных паролей в службе синхронизации Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Использование только локальной службы Active Directory или Active Directory на виртуальных машинах IaaS, без Azure AD и Azure AD DS, не поддерживается для кластеров HDInsight с ESP.

## <a name="next-steps"></a>Дополнительная информация

* [Настройка кластера HDInsight с корпоративным пакетом безопасности с помощью доменных служб Azure Active Directory](apache-domain-joined-configure-using-azure-adds.md)
* [Настройка политик Hive в кластерах HDInsight с ESP](apache-domain-joined-run-hive.md)
* [Настройка кластеров HDInsight с помощью ESP](apache-domain-joined-manage.md) 
