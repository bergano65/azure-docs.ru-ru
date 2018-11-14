---
title: Ссылки на дополнительные ресурсы об Avere vFXT для Azure
description: Ссылки на дополнительную информацию об Avere vFXT для Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958846"
---
# <a name="additional-documentation"></a>Дополнительная документация

В этой статье содержатся ссылки на дополнительную документацию, посвященную интерфейсу панели управления Avere и связанным темам. 

## <a name="avere-cluster-documentation"></a>Документация по кластеру Avere

Дополнительную документацию по кластеру Avere можно найти на веб-сайте <http://library.averesystems.com/>. Эти документы помогут понять возможности кластера и настроить его параметры. 

* [Руководство по созданию кластера FXT](<http://library.averesystems.com/#fxt_cluster>) предназначено для кластеров, состоящих из узлов физического устройства, однако некоторые сведения в этом документе также относятся к кластерам vFXT. В частности новым администраторам кластера vFXT может пригодиться информация из следующих разделов:
  * В разделе [Customizing Support and Monitoring Settings for the Avere Cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) (Настройка параметров поддержки и мониторинга для кластера Avere) объясняется, как настроить параметры отправки сведений для службы поддержки и включить удаленный мониторинг. 
  * В разделе [Configuring VServers and Global Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) (Настройка VServer и глобального пространства имен) содержатся сведения о создании пространства имен для клиентов.
  * В разделе [Configuring DNS for the Avere Cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) (Настройка DNS для кластера Avere) описывается настройка DNS циклического перебора.
  * В разделе [Adding Backend Storage](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) (Добавление серверного хранилища) описывается, как добавить основные файловые хранилища.

* [Руководство по настройке кластера](<http://library.averesystems.com/#operations>) — это полный перечень настроек и параметров для кластера Avere. Кластер vFXT использует подмножество этих параметров, однако большая часть страниц конфигурации применима.

* [Руководство по панели мониторинга](<http://library.averesystems.com/#operations>) содержит сведения об использовании функции мониторинга кластера на панели управления Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Документация по созданию и администрированию кластера vFXT

Полное руководство по использованию vfxt.py, служебной программы для создания и администрирования облачного кластера, приведено на сайте GitHub: [Cloud cluster management with vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md) (Управление облачным кластером с помощью vfxt.py).  
