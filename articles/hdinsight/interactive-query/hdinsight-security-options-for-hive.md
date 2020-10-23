---
title: Параметры безопасности для Hive в Azure HDInsight
description: Параметры безопасности для Hive в кластерах Standard и ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222654"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Параметры безопасности для Hive в Azure HDInsight

В этом документе описаны рекомендуемые параметры безопасности для Hive в HDInsight. Эти параметры можно настроить с помощью Ambari.

!["Параметры безопасности для Hive"](./media/hdinsight-security-options-for-hive/security-options-hive.png "Параметры безопасности для Hive")

## <a name="hiveserver2-authentication"></a>Проверка подлинности HiveServer2

Для стандартных кластеров рекомендуемым параметром для проверки подлинности HiveServer2 является значение по умолчанию None. Чтобы включить проверку подлинности, рекомендуется выполнить обновление до кластера [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) (корпоративный пакет безопасности). 

Для кластеров ESP проверка подлинности [Kerberos](https://web.mit.edu/Kerberos/) включена по умолчанию. Подключаемые модули проверки подлинности (PAM) и пользовательские схемы проверки подлинности не поддерживаются.

## <a name="hiveserver2-authorization"></a>Авторизация HiveServer2

Для стандартных кластеров значение по умолчанию — None. [Склстдаус (авторизация на основе стандартов SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) можно включить. Авторизация через [Apache Ranger](https://ranger.apache.org/) не поддерживается для стандартных кластеров. Мы рекомендуем выполнить обновление до кластера ESP для Ranger авторизации. 

Для кластеров ESP Авторизация через Ranger включена по умолчанию. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-шифрование для HiveServer2

Включение Hiveserver2 SSL не рекомендуется для обоих кластеров Standard или ESP. Вместо этого на шлюзе включается протокол SSL. [Шифрование при передаче](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) можно включить для шифрования обмена данными между узлами кластера по [протоколу IPsec](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Дальнейшие действия
* [Общие сведения о проверке подлинности HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Обзор авторизации HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [Включение авторизации Hive на основе стандартов SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger с Hive](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
