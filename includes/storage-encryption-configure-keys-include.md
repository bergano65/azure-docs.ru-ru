---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665918"
---
Служба хранилища Azure шифрует все данные в неактивных учетных записях хранения. По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, на уровне учетной записи хранения.

Ключи, управляемые клиентом, должны храниться в Azure Key Vault. Можно либо создать собственные ключи и сохранить их в хранилище ключей, либо использовать Azure Key Vault API для создания ключей. Учетная запись хранения и Key Vault должны быть расположены в одном регионе, но могут находиться в разных подписках. Дополнительные сведения о шифровании службы хранилища Azure и управлении ключами см. [в статье шифрование службы хранилища Azure для неактивных данных](../articles/storage/common/storage-service-encryption.md). Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](../articles/key-vault/key-vault-overview.md)
