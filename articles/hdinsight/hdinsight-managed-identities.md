---
title: Управляемые идентификаторы в Azure HDInsight
description: Предоставляет обзор реализации управляемых идентификаторов в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206016"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Управляемые идентификаторы в Azure HDInsight

Управляемая идентификация — это идентификация, зарегистрированная в Active Directory Azure (Azure AD), учетные данные которого управляются Azure. При управлении учетными данным не нужно регистрировать принципы служб в Azure AD или сохранять учетные данные, такие как сертификаты.

Управляемые идентификаторы используются в Azure HDInsight для доступа к службам домена Azure AD или доступа к файлам в Azure Data Lake Storage Gen2, когда это необходимо.

Существует два типа управляемых идентификационных данных: назначенных пользователем и назначенных системами. Azure HDInsight поддерживает только назначенные пользователем управляемые идентификаторы. HDInsight не поддерживает назначенные системой управляемые идентификаторы. Назначенное пользователем управляемое удостоверение создается как автономный ресурс Azure, который можно назначить одному или несколько экземплярам службы Azure. Напротив, в Azure AD создается система, назначенная управляемым удостоверением, а затем автоматически включена непосредственно в определенном экземпляре службы Azure. Срок службы этой системы, назначенной управляемой, затем привязан к сроку службы экземпляра, на котором она включена.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight управляет реализацией идентификационных данных

В Azure HDInsight управляемые идентификаторы готовятся на каждом узеле кластера. Эти компоненты идентификации, однако, могут быть пригодны только службой HDInsight. В настоящее время нет поддерживаемого метода для генерации токенов доступа с помощью управляемых идентификаторов, установленных на кластерных узлах HDInsight. Для некоторых служб Azure управляемые идентификаторы реализуются с конечной точкой, которую можно использовать для приобретения токенов доступа для взаимодействия с другими службами Azure самостоятельно.

## <a name="create-a-managed-identity"></a>Создание управляемой идентификации

Управляемые идентификаторы могут быть созданы с помощью любого из следующих методов:

* [Портал Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Лазурная силаШелл](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Лазурный CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Остальные этапы настройки управляемого удостоверения зависят от сценария, в котором она будет использоваться.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Управляемые сценарии идентификации в Azure HDInsight

Управляемые идентификаторы используются в Azure HDInsight в нескольких сценариях. Ознакомьтесь с соответствующими документами для получения подробных инструкций по настройке и конфигурации:

* [Лазурное хранилище озер данных Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Пакет корпоративной безопасности](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Шифрование управляемых клиентом ключей](disk-encryption.md)

## <a name="faq"></a>часто задаваемые вопросы
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Что произойдет, если я удалю управляемую идентификацию после создания кластера?
Кластер столкнется с проблемами, когда потребуется управляемое удостоверение. В настоящее время нет способа обновить или изменить интилики управления после создания кластера. Поэтому наша рекомендация состоит в том, чтобы убедиться, что управляемое удостоверение не удаляется во время выполнения кластера. Кроме того, можно воссоздать кластер и назначить новую управляемую идентификацию.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)
