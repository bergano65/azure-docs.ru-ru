---
title: Схема NetworkTrafficRules определения облачных служб Azure | Документация Майкрософт
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613887"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Схема NetworkTrafficRules определения облачных служб Azure
Узел `NetworkTrafficRules` — это необязательный элемент в файле определения службы, который указывает, как роли взаимодействуют друг с другом. Он ограничивает множество ролей, которые могут обращаться ко внутренним конечным точкам определенной роли. `NetworkTrafficRules` не является отдельным элементом. Он объединяется с двумя или более ролями в файле определения службы.

Расширение по умолчанию для файла определения службы — .csdef.

> [!NOTE]
>  Узел `NetworkTrafficRules` доступен только при использовании пакета Azure SDK версии 1.3 или выше.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Базовая схема определения службы для правил сетевого трафика
Ниже приведен базовый формат файла определения службы, содержащий определения сетевого трафика.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Элементы схемы
Узел `NetworkTrafficRules` файла определения службы содержит следующие элементы, которые подробно описаны в этой статье:

[Элемент NetworkTrafficRules](#NetworkTrafficRules)

[Элемент OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Элемент Destinations](#Destinations)

[Элемент RoleEndpoint](#RoleEndpoint)

Элемент AllowAllTraffic

[Элемент WhenSource](#WhenSource)

[Элемент FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a> Элемент NetworkTrafficRules
Элемент `NetworkTrafficRules` определяет роли, которые могут обмениваться данными с определенной конечной точкой в другой роли. Служба может содержать одно определение `NetworkTrafficRules`.

##  <a name="OnlyAllowTrafficTo"></a> Элемент OnlyAllowTrafficTo
Элемент `OnlyAllowTrafficTo` описывает коллекцию целевых конечных точек и ролей, которые могут обмениваться данными с ними. Можно указать несколько узлов `OnlyAllowTrafficTo`.

##  <a name="Destinations"></a> Элемент Destinations
Элемент `Destinations` описывает коллекцию элементов RoleEndpoint, с которыми можно обмениваться данными.

##  <a name="RoleEndpoint"></a> Элемент RoleEndpoint
Элемент `RoleEndpoint` описывает конечную точку в роли, с которой можно обмениваться данными. Можно указать несколько элементов `RoleEndpoint`, если в роли существует несколько конечных точек.

| Атрибут      | type     | Описание |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Обязательный. Имя конечной точки, к которой разрешается трафик.|
| `roleName`     | `string` | Обязательный. Имя веб-роли, с которой разрешается обмен данными.|

## <a name="allowalltraffic-element"></a>Элемент AllowAllTraffic
Элемент `AllowAllTraffic` — это правило, которое разрешает всем ролям обмениваться данными с конечными точками, определенными в узле `Destinations`.

##  <a name="WhenSource"></a> Элемент WhenSource
Элемент `WhenSource` описывает коллекцию ролей, которые могут обмениваться данными с конечными точками, определенными в узле `Destinations`.

| Атрибут | type     | Описание |
| --------- | -------- | ----------- |
| `matches` | `string` | Обязательный. Указывает правило, применяемое при разрешении обмена данными. Сейчас единственное допустимое значение — `AnyRule`.|
  
##  <a name="FromRole"></a> Элемент FromRole
Элемент `FromRole` указывает роли, которые могут обмениваться данными с конечными точками, определенными в узле `Destinations`. Можно указать несколько элементов `FromRole`, если существует несколько ролей, которые могут обмениваться данными с конечными точками.

| Атрибут  | type     | Описание |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Обязательный. Имя роли, из которой разрешается обмен данными.|

## <a name="see-also"></a>См. также
[Схема определения облачных служб (классических)](schema-csdef-file.md).
