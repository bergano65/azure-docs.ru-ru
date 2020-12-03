---
title: Создание учетных данных для проверок и управление ими
description: В этой статье описаны действия по созданию учетных данных в Azure зрения и управлению ими.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553445"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Учетные данные для исходной проверки подлинности в Azure зрения

В этой статье описывается, как создать учетные данные в Azure зрения для быстрого повторного использования и применения сохраненных данных проверки подлинности к проверкам источника данных.

## <a name="prerequisites"></a>Предварительные требования

* Хранилище ключей Azure. Если у вас ее нет, то как (вставить ссылку на статью о создании KV), чтобы создать ее.

## <a name="introduction"></a>Введение
Учетные данные — это данные проверки подлинности, которые Azure зрения может использовать для проверки подлинности в зарегистрированных источниках данных. Объект учетных данных может быть создан для различных типов сценариев проверки подлинности (например, для обычной проверки подлинности, требующих имени пользователя и пароля), и будет записывать конкретные сведения, необходимые в зависимости от выбранного типа метода проверки подлинности. Учетные данные используют существующие секреты хранилища ключей Azure для получения конфиденциальных сведений о проверке подлинности в процессе создания учетных данных.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Использование управляемого удостоверения зрения для настройки проверок
Если для настройки проверок используется управляемое удостоверение зрения, вам не потребуется явно создавать учетные данные и связывать хранилище ключей с зрения для их хранения. Подробные инструкции по добавлению управляемого удостоверения зрения для получения доступа к просмотру источников данных см. в следующих разделах, посвященных проверке подлинности в источнике данных.

- [Хранилище BLOB-объектов Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage 1-го поколения](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage 2-го поколения](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [База данных SQL Azure](register-scan-azure-sql-database.md)
- [Управляемый экземпляр Базы данных SQL Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Создание подключений Azure Key Vault в учетной записи Azure зрения

Прежде чем можно будет создать учетные данные, сначала необходимо связать один или несколько существующих экземпляров Azure Key Vault с учетной записью Azure зрения.

1. В меню навигации зрения Azure перейдите в центр управления и перейдите к учетным данным.

2. На панели команд учетные данные выберите Управление подключениями Key Vault.

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Управление подключениями AKV":::

3. На панели Управление подключениями Key Vault выберите + Создать. 

4. Укажите необходимые сведения, а затем выберите Создать.

5. Убедитесь, что ваша Key Vault успешно связана с вашей учетной записью Azure зрения.

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Просмотр подключений AKV":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Предоставьте управляемому удостоверению зрения доступ к вашей Azure Key Vault

Перейдите к хранилищу ключей — > политикам доступа — > добавить политику доступа. Предоставьте разрешение Get в раскрывающемся списке разрешения секретов и выберите субъект в качестве MSI зрения. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Добавление зрения MSI в AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Добавление политики доступа":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Сохранить политику доступа":::

## <a name="create-a-new-credential"></a>Создание новых учетных данных

Тип учетных данных, поддерживаемый в зрения уже сегодня:
* Обычная проверка подлинности: вы добавите **пароль** в качестве секрета в хранилище ключей.
* Субъект-служба. вы добавите **ключ субъекта-службы** в качестве секрета в хранилище ключей. 
* Проверка подлинности SQL. вы добавите **пароль** в качестве секрета в хранилище ключей.
* Ключ учетной записи. вы добавите **ключ учетной записи** в качестве секрета в хранилище ключей.

Ниже приведены дополнительные сведения о том, как добавить секреты в хранилище ключей: (вставить статью о хранилище ключей)

После сохранения секретов в хранилище ключей создайте новые учетные данные, выбрав + создать на панели команд учетные данные. Укажите необходимые сведения, включая выбор метода проверки подлинности и экземпляра Key Vault, из которого следует выбрать секрет. После заполнения всех сведений щелкните Создать.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Новые учетные данные":::

Убедитесь, что новые учетные данные отображаются в представлении списка учетных данных и готовы к использованию.

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Просмотр учетных данных":::

## <a name="manage-your-key-vault-connections"></a>Управление подключениями к хранилищу ключей

1. Поиск и поиск Key Vault подключений по имени

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Поиск хранилища ключей":::

1. Удаление одного или нескольких подключений Key Vault
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Удаление хранилища ключей":::

## <a name="manage-your-credentials"></a>Управление учетными данными

1. Поиск и поиск учетных данных по имени
  
2. Выбор и обновление существующих учетных данных

3. Удаление одного или нескольких учетных данных