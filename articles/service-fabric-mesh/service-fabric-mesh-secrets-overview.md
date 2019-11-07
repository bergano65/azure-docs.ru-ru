---
title: Хранение и использование секретов приложения в службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Вот как можно хранить секреты и управлять ими с помощью приложений Service Fabricной сетки.
services: service-fabric-mesh
keywords: секретные коды
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72188517c237b170b709c48f16d3c131985f95d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686243"
---
# <a name="service-fabric-mesh-application-secrets"></a>Секреты приложения в службе "Сетка Service Fabric"
Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Секретом в службе "Сетка Service Fabric" может быть любая конфиденциальная текстовая информация, например строки подключения к хранилищу, пароли или другие значения, которые должны хранится и передаваться защищенными.

![Общие сведения секретах в Сетке][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ресурсы секретов Сетки
Секрет приложения сетки состоит из следующих компонентов.
* Ресурс **Секреты** — это контейнер, в котором хранятся секреты в текстовом формате. Секреты, находящиеся в пределах ресурса **Секреты**, хранятся и передаются в защищенном виде.
* Один или несколько ресурсов **Секреты** (или Значения), которые хранятся в контейнере ресурса **Секреты**. Каждый ресурс **Секреты/Значения** отличается номером версии.

## <a name="next-steps"></a>Дальнейшие действия 
Дополнительные сведения о секретах в службе "Сетка Service Fabric".
- [Управление секретами приложения в службе "Сетка Service Fabric"](service-fabric-mesh-howto-manage-secrets.md)
- [Общие сведения о модели ресурсов Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
