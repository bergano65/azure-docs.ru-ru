---
title: Хранение и использование секретов приложения в службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: Хранение и использование секретов в службе "Сетка Service Fabric".
services: service-fabric-mesh
keywords: секретные коды
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891978"
---
# <a name="service-fabric-mesh-application-secrets"></a>Секреты приложения в службе "Сетка Service Fabric"
Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Секретом в службе "Сетка Service Fabric" может быть любая конфиденциальная текстовая информация, например строки подключения к хранилищу, пароли или другие значения, которые должны хранится и передаваться защищенными.

![Общие сведения секретах в Сетке][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ресурсы секретов Сетки
Секрет приложения в Сетке состоит из следующих компонентов:
* Ресурс **Секреты** — это контейнер, в котором хранятся секреты в текстовом формате. Секреты, находящиеся в пределах ресурса **Секреты**, хранятся и передаются в защищенном виде.
* Один или несколько ресурсов **Секреты** (или Значения), которые хранятся в контейнере ресурса **Секреты**. Каждый ресурс **Секреты** (или Значения) отличается номером версии.

## <a name="inline-or-stored-in-azure-key-vault"></a>Ключи, встроенные или хранящиеся в Azure Key Vault
- Приложения в службе "Сетка" и ресурсы службы содержат Управляемое удостоверение службы (MSI) и Azure Active Directory (AAD), чтобы иметь возможность обращаться к секретам в Azure Key Vault.
- Секреты и сертификаты могут автоматически изменяться вместе с политиками.

## <a name="next-steps"></a>Дополнительная информация 
Дополнительные сведения о секретах в службе "Сетка Service Fabric".
- [Управление секретами приложения в службе "Сетка Service Fabric"](service-fabric-mesh-howto-manage-secrets.md)
- [Общие сведения о модели ресурсов Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
