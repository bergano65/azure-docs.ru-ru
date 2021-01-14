---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dd9743f98959f5590198b752c0c2c0037b3d2b28
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046450"
---
|Имя |Описание |Политики |Версия |
|---|---|---|---|
|[Аудит компьютеров с ненадежными параметрами безопасности паролей](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Эта инициатива развертывает требуемые компоненты политики и осуществляет аудит компьютеров с ненадежными параметрами безопасности пароля. Дополнительные сведения о политиках гостевой конфигурации: [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Развернуть необходимые компоненты, чтобы включить политики гостевой конфигурации на виртуальных машинах](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Эта инициатива добавляет управляемое удостоверение, назначаемое системой, и развертывает соответствующее платформе расширение гостевой конфигурации на виртуальных машинах, которые могут отслеживаться с помощью политик гостевой конфигурации. Это обязательный компонент для всех политик гостевой конфигурации, который необходимо назначить области назначения политики перед использованием любой политики гостевой конфигурации. Дополнительные сведения о гостевой конфигурации см. на странице [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0 (предварительная версия) |
|[Компьютеры под управлением Windows должны соответствовать требованиям для базовой конфигурации безопасности Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Эта инициатива осуществляет аудит компьютеров под управлением Windows с параметрами, не соответствующими базовой конфигурации безопасности Azure. Дополнительные сведения см. по адресу [https://aka.ms/gcpol](https://aka.ms/gcpol). |29 |2.0.0-preview |
