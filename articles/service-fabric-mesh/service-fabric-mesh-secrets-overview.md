---
title: Хранение и использование секретов приложения сети Service Fabric Azure
description: Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Вот как можно хранить секреты и управлять ими с помощью приложений Service Fabricной сетки.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625538"
---
# <a name="service-fabric-mesh-application-secrets"></a>Секреты приложения в службе "Сетка Service Fabric"

> [!IMPORTANT]
> Предварительная версия сетки Service Fabric Azure была снята с учета. Новые развертывания больше не будут разрешены через интерфейс API Service Fabricной сетки. Поддержка существующих развертываний будет продолжена 28 апреля 2021 г.
> 
> Дополнительные сведения см. в статье о прекращении использования [предварительной версии сети Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Служба "Сетка Service Fabric" поддерживает секреты в качестве ресурсов Azure. Секретом в службе "Сетка Service Fabric" может быть любая конфиденциальная текстовая информация, например строки подключения к хранилищу, пароли или другие значения, которые должны хранится и передаваться защищенными.

![Общие сведения секретах в Сетке][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Ресурсы секретов Сетки
Секрет приложения в Сетке состоит из следующих компонентов:
* Ресурс **Секреты** — это контейнер, в котором хранятся секреты в текстовом формате. Секреты, находящиеся в пределах ресурса **Секреты**, хранятся и передаются в защищенном виде.
* Один или несколько ресурсов **Секреты** (или Значения), которые хранятся в контейнере ресурса **Секреты**. Каждый ресурс **Секреты** (или Значения) отличается номером версии.

## <a name="next-steps"></a>Дальнейшие действия 
Дополнительные сведения о секретах в службе "Сетка Service Fabric".
- [Управление секретами приложения "Сетка Service Fabric"](service-fabric-mesh-howto-manage-secrets.md)
- [Общие сведения о модели ресурсов Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
