---
title: Резервное копирование в Azure Key Vault | Документация Майкрософт
description: Этот документ поможет при резервном копировании секрета, ключа или сертификата, хранимых в Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156358"
---
# <a name="azure-key-vault-backup"></a>Резервное копирование в Azure Key Vault

Из этого документа вы узнаете, как выполнить резервное копирование отдельных секретов, ключей и сертификатов, хранимых в хранилище ключей. Такое резервное копирование позволяет создать автономную копию всех секретов в том маловероятном случае, если доступ к хранилищу ключей будет утерян.

## <a name="overview"></a>Обзор

Key Vault автоматически предоставляет несколько функций для поддержания доступности и предотвращения потери данных. Такое резервное копирование следует выполнять только при наличии бизнес-обоснования критической важности резервного копирования секретов. При резервном копировании секретов в хранилище ключей вам, возможно, потребуется выполнить дополнительные операционные задачи, такие как сохранение нескольких наборов журналов, разрешений и резервных копий по истечении срока действия или смене секретов.

Key Vault сохраняет доступность в аварийных сценариях и автоматически выполняет отработку отказа запросов в парный регион без вмешательства пользователя. Дополнительные сведения см. по приведенной ниже ссылке. [Аварийное восстановление в Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

С помощью функций обратимого удаления и защиты от очистки Key Vault защищает секреты от случайного и намеренного вредоносного удаления. Если вам требуется защита от случайного или злонамеренного удаления секретов, настройте функции обратимого удаления и очистки в хранилище ключей. Дополнительные сведения приведены в указанном ниже документе. [Восстановление в Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Ограничения

Сейчас Azure Key Vault не поддерживает резервное копирование всего хранилища ключей с помощью одной операции. Корпорация Майкрософт и команда Azure Key Vault не поддерживает любые попытки использовать команды, перечисленные в этом документе, для выполнения автоматического резервного копирования хранилища ключей.

Попытка использовать команды, приведенные в документе ниже для создания пользовательской автоматизации, может привести к ошибкам.

* Резервное копирование секретов с несколькими версиями может привести к ошибкам времени ожидания.
* При резервном копировании создается моментальный снимок на определенный момент времени. Во время резервного копирования секреты могут обновиться, что приведет к несоответствию ключей шифрования.
* Если ограничения службы хранилища ключей для количества запросов в секунду будут превышены, будет выполнено регулирование и создать резервную копию не удастся.

## <a name="design-considerations"></a>Рекомендации по проектированию

При резервном копировании объекта, хранимого в хранилище ключей (секрета, ключа или сертификата), он скачивается как зашифрованный большой двоичный объект. Этот большой двоичный объект нельзя расшифровать за пределами Azure. Чтобы получить пригодные для использования данные из этого большого двоичного объекта, необходимо восстановить его в хранилище ключей в той же подписке и географической области Azure.
[Географические области Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Предварительные требования

* Разрешения уровня участника или более высокого уровня в подписке Azure.
* Первичное хранилище ключей, содержащее секреты, резервные копии которых нужно создать.
* Вторичное хранилище ключей, в котором будут восстановлены секреты.

## <a name="back-up-and-restore-with-azure-portal"></a>Резервное копирование и восстановление с помощью портала Azure

### <a name="back-up"></a>Резервное копирование

1. Перейдите на портал Azure.
2. Выберите нужное хранилище ключей.
3. Перейдите к объекту (секрету, ключу или сертификату), резервную копию которого нужно создать.

    ![Образ —](../media/backup-1.png)

4. Выберите объект.
5. Выберите "Скачать резервную копию".

    ![Образ —](../media/backup-2.png)
    
6. Нажмите кнопку "Скачать".

    ![Образ —](../media/backup-3.png)
    
7. Храните зашифрованный большой двоичный объект в безопасном расположении.

### <a name="restore"></a>Восстановить

1. Перейдите на портал Azure.
2. Выберите нужное хранилище ключей.
3. Перейдите к типу объекта (секрет, ключ или сертификат), который нужно восстановить.
4. Выберите "Восстановить резервную копию".

    ![Образ —](../media/backup-4.png)
    
5. Перейдите к расположению, где сохранен зашифрованный большой двоичный объект.
6. Нажмите «ОК».

## <a name="back-up-and-restore-with-the-azure-cli"></a>Резервное копирование и восстановление с помощью Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Дальнейшие действия

Включите ведение журнала и мониторинг для Azure Key Vault. [Ведение журнала хранилища ключей Azure](https://docs.microsoft.com/azure/key-vault/general/logging)
