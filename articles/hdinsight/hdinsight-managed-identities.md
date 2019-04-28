---
title: Управляемые удостоверения в Azure HDInsight
description: Обзор реализации управляемыми удостоверениями в Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 5012b669b7460a44cb2732d7db7bf76fd1f567cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766936"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Управляемые удостоверения в Azure HDInsight

Управляемое удостоверение является удостоверением зарегистрирован в Azure Active Directory (Azure AD), учетные данные управляются Azure. Управляемые удостоверения не требуется для регистрации субъектов-служб в Azure AD или сохранить учетные данные, такие как сертификаты.

Управляемые удостоверения можно использовать в Azure HDInsight, чтобы разрешить своими кластерами, чтобы доступ к доменным службам Azure AD, доступ к хранилищу ключей Azure или получить доступ к файлам в Gen2 хранилища Озера данных Azure.

Существует два типа управляемых удостоверений: управляемого удостоверения и назначенное системой. Azure HDInsight использует назначаемого пользователем управляемого удостоверения. Назначаемое пользователем управляемое удостоверение создается как отдельный ресурс Azure, который затем можно назначить один или несколько экземпляров службы Azure. Напротив назначенный системой управляемого удостоверения создается в Azure AD и автоматически включена непосредственно в экземпляре конкретной службы Azure. Жизненного цикла, назначенный системой управляемого удостоверения затем привязывается к жизненного цикла экземпляра службы, где она включена.

## <a name="hdinsight-managed-identity-implementation"></a>Реализация HDInsight управляемого удостоверения

В Azure HDInsight управляемых удостоверений подготавливаются на каждом узле кластера. Эти компоненты удостоверений, однако используются только службой HDInsight. В данный момент не поддерживается для создания маркеров доступа, с помощью управляемых удостоверений, которые установлены на узлах кластера HDInsight. Для некоторых служб Azure управляемых удостоверений реализуются с помощью конечной точки, можно использовать для получения маркеров доступа для взаимодействия с другими службами Azure самостоятельно.

## <a name="create-a-managed-identity"></a>Создание управляемого удостоверения

Управляемые удостоверения могут создаваться с любым из следующих методов:

* [портал Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Интерфейс командной строки Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Оставшиеся шаги по настройке управляемого удостоверения зависит от сценария, где она будет использоваться.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Сценарии управляемого удостоверения в Azure HDInsight

Управляемые удостоверения используются в Azure HDInsight в различных сценариях. См. в разделе связанные документы для детальной настройки и инструкции по настройке:

* [Хранилище Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Пакет безопасности корпоративного уровня](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Создание собственных ключей (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)
