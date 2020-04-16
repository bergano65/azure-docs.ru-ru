---
title: Управляемые идентификаторы в Azure HDInsight
description: Предоставляет обзор реализации управляемых идентификаторов в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408938"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Управляемые идентификаторы в Azure HDInsight

Управляемая идентификация — это идентификация, зарегистрированная в Active Directory Azure (Azure AD), учетные данные которого управляются Azure. С управляемыми учетными данными не нужно регистрировать принципы служб в Azure AD. Или сохранить учетные данные, такие как сертификаты.

Управляемые идентификаторы используются в Azure HDInsight для доступа к службам домена Azure AD или доступа к файлам в Azure Data Lake Storage Gen2, когда это необходимо.

Существует два типа управляемых идентификационных данных: назначенных пользователем и назначенных системами. Azure HDInsight поддерживает только назначенные пользователем управляемые идентификаторы. HDInsight не поддерживает системные управляемые идентификаторы. Назначенное пользователем управляемое удостоверение создается как автономный ресурс Azure, который можно назначить одному или несколько экземплярам службы Azure. Напротив, в Azure AD создается система, назначенная управляемым удостоверением, а затем автоматически включена непосредственно в определенном экземпляре службы Azure. Срок службы этой системы, назначенной управляемой, затем привязан к сроку службы экземпляра, на котором она включена.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight управляет реализацией идентификационных данных

В Azure HDInsight управляемые идентификаторы готовятся на каждом узеле кластера. Эти компоненты идентификации, однако, могут быть пригодны только службой HDInsight. В настоящее время нет поддерживаемого метода для генерации токенов доступа с помощью управляемых идентификаторов, установленных на кластерных узлах HDInsight. Для некоторых служб Azure управляемые идентификаторы реализуются с конечной точкой, которую можно использовать для получения токенов доступа. Используйте токены для самостоятельного взаимодействия с другими службами Azure.

## <a name="create-a-managed-identity"></a>Создание управляемой идентификации

Управляемые идентификаторы могут быть созданы с помощью любого из следующих методов:

* [Портал Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Остальные этапы настройки управляемого удостоверения зависят от сценария, в котором она будет использоваться.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Управляемые сценарии идентификации в Azure HDInsight

Управляемые идентификаторы используются в Azure HDInsight в нескольких сценариях. Ознакомьтесь с соответствующими документами для получения подробных инструкций по настройке и конфигурации:

* [Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Пакет корпоративной безопасности](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Шифрование управляемых клиентом ключей](disk-encryption.md)

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Что произойдет, если я удалю управляемую идентификацию после создания кластера?

Кластер столкнется с проблемами, когда потребуется управляемое удостоверение. В настоящее время невозможно обновить или изменить управляемый иждивенный номер после создания кластера. Поэтому наша рекомендация состоит в том, чтобы убедиться, что управляемое удостоверение не удаляется во время выполнения кластера. Или можно воссоздать кластер и назначить новую управляемую идентификацию.

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)
