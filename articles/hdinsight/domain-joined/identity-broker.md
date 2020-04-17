---
title: Используйте ID Broker (предварительный просмотр) для управления учетными данными - Azure HDInsight
description: Узнайте о HDInsight ID Broker для упрощения аутентификации для кластеров Apache Hadoop, присоединенных к домену.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: fb82cec6874f8ef4f41897cc22939fe69ed02ec2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457422"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Используйте ID Broker (предварительный просмотр) для управления учетных данных

В этой статье описывается, как настроить и использовать функцию ID Broker в Azure HDInsight. Эту функцию можно использовать для входной связи в Apache Ambari через Azure Multi-Factor Authentication и получить необходимые билеты Kerberos без необходимости использования хэшов паролей в службах домена Active Directory (Azure AD DS).

## <a name="overview"></a>Обзор

ID Broker упрощает сложные настройки аутентификации в следующих сценариях:

* Ваша организация полагается на федерацию для проверки подлинности пользователей для доступа к облачным ресурсам. Ранее для использования кластеров HDInsight Enterprise Security Package (ESP) необходимо было включить синхронизацию хэша паролей из среды в предварительной среде в Active Directory Azure. Это требование может быть трудным или нежелательным для некоторых организаций.

* Вы строите решения, которые используют технологии, которые опираются на различные механизмы аутентификации. Например, Apache Hadoop и Apache Ranger полагаются на Kerberos, в то время как Хранилище озер данных Azure data опирается на OAuth.

ID Broker предоставляет единую инфраструктуру аутентификации и удаляет требование синхронизации хэшов паролей с Azure AD DS. ID Broker состоит из компонентов, работающих на Windows Server VM (узло ID Broker), а также узлов шлюза кластера. 

Следующая диаграмма показывает поток аутентификации для всех пользователей, включая федеративных пользователей, после включения ID Broker:

![Поток аутентификации с ID Брокер](./media/identity-broker/identity-broker-architecture.png)

ID Broker позволяет войти в кластеры ESP с помощью Multi-Factor Authentication, не предоставляя никаких паролей. Если вы уже зарегистрировались в других службах Azure, таких как портал Azure, вы можете войти в свой кластер HDInsight с одним интерфейсом регистрации (SSO).

## <a name="enable-hdinsight-id-broker"></a>Включить HDInsight ID Брокер

Чтобы создать кластер ESP с включенным ID Broker, следующие шаги:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Следуйте основным шагам создания кластера ESP. Для получения дополнительной информации [см.](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)
1. Выберите **Включить HDInsight ID брокера**.

Функция ID Broker добавит в кластер еще один дополнительный VM. Этот VM является узлом ID Broker и включает в себя компоненты сервера для поддержки аутентификации. Узел ID Broker — это домен, присоединенный к домену Azure AD DS.

![Вариант для включения ID Брокер](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Интеграция инструментов

Плагин HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) обновляется для поддержки OAuth. Этот плагин можно использовать для подключения к кластеру и отправки заданий.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Доступ SSH без хэша пароля в Azure AD DS

После включения ID Broker все равно потребуется хэш-хэш паролей, хэшированный в Azure AD DS для sSH-сценариев с учетными записями доменов. Чтобы SSH к домену, связанного с `kinit` VM, или запустить команду, необходимо предоставить пароль. 

Проверка подлинности SSH требует, чтобы хэш был доступен в Azure AD DS. Если вы хотите использовать SSH только для административных сценариев, можно создать одну учетную запись только для облака и использовать ее для SSH в кластере. Другие пользователи по-прежнему могут использовать инструменты Ambari или HDInsight (например, плагин IntelliJ) без наличия хэша паролей в Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Клиенты, использующие OAuth для подключения к шлюзу HDInsight с установкой ID Broker

В установке ID брокера пользовательские приложения и клиенты, подключающиеся к шлюзу, могут быть обновлены для приобретения необходимого токена OAuth в первую очередь. Вы можете следовать шагам в этом [документе,](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) чтобы приобрести токен со следующей информацией:

*   OAuth ресурс ури:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Разрешение: (имя: Cluster.ReadWrite, id:8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Следующие шаги

* [Налажить кластер HDInsight с пакетом корпоративной безопасности с помощью служб домена Active Directory Azure](apache-domain-joined-configure-using-azure-adds.md)
* [Синхронизация пользователей Azure Active Directory с кластером HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Мониторинг производительности кластера](../hdinsight-key-scenarios-to-monitor.md)
