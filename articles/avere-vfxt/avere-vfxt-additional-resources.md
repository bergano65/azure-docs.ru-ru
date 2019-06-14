---
title: Ссылки на дополнительные ресурсы об Avere vFXT для Azure
description: Ссылки на дополнительную информацию об Avere vFXT для Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515599"
---
# <a name="additional-documentation"></a>Дополнительная документация

В этой статье содержатся ссылки на дополнительную документацию, посвященную интерфейсу панели управления Avere и связанным темам. 

## <a name="avere-cluster-documentation"></a>Документация по кластеру Avere

Дополнительную документацию по кластеру Avere можно найти на веб-сайте <https://azure.github.io/Avere/>. Эти документы помогут понять возможности кластера и настроить его параметры. 

* [Руководство по созданию кластера FXT](<https://azure.github.io/Avere/#fxt_cluster>) предназначено для кластеров, состоящих из узлов физического устройства, однако некоторые сведения в этом документе также относятся к кластерам vFXT. В частности новым администраторам кластера vFXT может пригодиться информация из следующих разделов:
  * В разделе [Customizing Support and Monitoring Settings for the Avere Cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) (Настройка параметров поддержки и мониторинга для кластера Avere) объясняется, как настроить параметры отправки сведений для службы поддержки и включить удаленный мониторинг. 
  * В разделе [Configuring VServers and Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) (Настройка VServer и глобального пространства имен) содержатся сведения о создании пространства имен для клиентов.
  * В разделе [Configuring DNS for the Avere Cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) (Настройка DNS для кластера Avere) описывается настройка DNS циклического перебора.
  * В разделе [Adding Backend Storage](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) (Добавление серверного хранилища) описывается, как добавить основные файловые хранилища.

* [Руководство по настройке кластера](<https://azure.github.io/Avere/#operations>) — это полный перечень настроек и параметров для кластера Avere. Кластер vFXT использует подмножество этих параметров, однако большая часть страниц конфигурации применима.

* [Руководство по панели мониторинга](<https://azure.github.io/Avere/#operations>) содержит сведения об использовании функции мониторинга кластера на панели управления Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Документация по созданию и администрированию кластера vFXT

Полное руководство по использованию vfxt.py, служебной программы для создания и администрирования облачного кластера, приведено на странице GitHub: [Cloud cluster management with vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md) (Управление облачным кластером с помощью vfxt.py).  
