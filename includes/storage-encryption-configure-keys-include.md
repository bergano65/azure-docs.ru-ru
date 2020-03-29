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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895288"
---
Azure Storage шифрует все данные в учетной записи хранения в состоянии покоя. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного контроля над ключами шифрования можно предоставить управляемые клиентом ключи для использования для шифрования кабли и файловых данных.

Ключи, управляемые клиентом, должны храниться в Хранилище ключей Azure. Вы можете создать свои собственные ключи и хранить их в хранилище ключей, либо использовать AA-иносы Azure Key Vault для генерации ключей. Учетная запись хранения и Key Vault должны быть расположены в одном регионе, но могут находиться в разных подписках. Для получения дополнительной информации о шифровании [Azure Storage encryption for data at rest](../articles/storage/common/storage-service-encryption.md)хранения Azure и управлении ключами см. Для получения дополнительной информации о [What is Azure Key Vault?](../articles/key-vault/key-vault-overview.md) Убежище ключей Azure см.
