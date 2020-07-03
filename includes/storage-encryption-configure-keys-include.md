---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895288"
---
Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, для шифрования больших двоичных объектов и данных файлов.

Ключи, управляемые клиентом, должны храниться в Azure Key Vault. Можно либо создать собственные ключи и сохранить их в хранилище ключей, либо использовать Azure Key Vault API для создания ключей. Учетная запись хранения и Key Vault должны быть расположены в одном регионе, но могут находиться в разных подписках. Дополнительные сведения о шифровании службы хранилища Azure и управлении ключами см. [в статье шифрование службы хранилища Azure для неактивных данных](../articles/storage/common/storage-service-encryption.md). Дополнительные сведения о Azure Key Vault см. в разделе [что такое Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
