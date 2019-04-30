---
title: Хранение и использование секретов приложения в службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: Хранение и использование секретов в службе "Сетка Service Fabric".
services: service-fabric-mesh
keywords: секреты
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810653"
---
# <a name="service-fabric-mesh-application-secrets"></a>Секреты приложения в службе "Сетка Service Fabric"
Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Секретом в службе "Сетка Service Fabric" может быть любая конфиденциальная текстовая информация, например строки подключения к хранилищу, пароли или другие значения, которые должны хранится и передаваться защищенными.

![Общие сведения секретах в Сетке][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ресурсы секретов Сетки
Секрет приложения в Сетке состоит из следующих компонентов:
* Ресурс **Секреты** — это контейнер, в котором хранятся секреты в текстовом формате. Секреты, находящиеся в пределах ресурса **Секреты**, хранятся и передаются в защищенном виде.
* Один или несколько ресурсов **Секреты** (или Значения), которые хранятся в контейнере ресурса **Секреты**. Каждый ресурс **Секреты** (или Значения) отличается номером версии.

## <a name="next-steps"></a>Дальнейшие действия 
Дополнительные сведения о секретах в службе "Сетка Service Fabric".
- [Управление секретами приложения в службе "Сетка Service Fabric"](service-fabric-mesh-howto-manage-secrets.md)
- [Общие сведения о модели ресурсов Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
