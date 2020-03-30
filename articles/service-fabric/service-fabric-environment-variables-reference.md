---
title: Переменные среды службы Azure
description: Узнайте о переменных среды в структуре службы Azure. Содержит ссылку на полный список переменных и их использование.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645690"
---
# <a name="service-fabric-environment-variables"></a>Переменные среды Service Fabric

В Service Fabric есть встроенные переменные среды, задаваемые для каждого экземпляра службы. Полный список этих переменных среды приведен ниже.

| Переменная среды                         | Описание                                                            | Пример                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Универсальный код ресурса (URI) структуры для приложения.                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Имя пакета кода, которому принадлежит процесс.              | Код                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | IP-адрес или полное доменное имя конечной точки.                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Номер порта для конечной точки.                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Папка журналов.                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Временная папка.                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Рабочая папка.                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Домашняя папка приложений.                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Логическое значение, указывающее, является ли процесс контейнером.                   | false                                                                |
| Fabric_NodeId                                | Идентификатор узла, на котором выполняется процесс.                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP-адрес или полное доменное имя узла согласно файлу манифеста кластера. | localhost или 10.0.0.1                                                |
| Fabric_NodeName                              | Имя узла, на котором выполняется процесс.                          | _Node_0                                                              |
| Fabric_ServiceName                           | Универсальный код ресурса (URI) структуры для службы, если служба размещена в режиме ExclusiveProcess. Значение этой переменной доступно только в том случае, если служба создается с режимом ServicePackageActivationMode ExclusiveProcess.  | Fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | Идентификатор FabricServicePackageActivationId.                                         | Глобальный уникальный идентификатор.                                                               |
| Fabric_ServicePackageName                    | Имя пакета службы, частью которого является процесс.                     | Web1Pkg                                                              |

Внутренние переменные среды, используемые средой выполнения Service Fabric.

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
