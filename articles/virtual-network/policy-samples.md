---
title: Примеры шаблонов политики | Документация Майкрософт
description: Примеры шаблонов службы "Политика Azure" для виртуальной сети.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342089"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Примеры шаблонов службы "Политика Azure" для виртуальной сети

В таблице ниже указаны ссылки на примеры [Политики Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Эти примеры можно найти в [репозитории примеров службы "Политика Azure"](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Сеть**||
| [Конкретная группа безопасности сети на каждой сетевой карте](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы определенная группа безопасности сети использовалась для каждого виртуального сетевого интерфейса. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Конкретная группа безопасности сети в каждой подсети](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы определенная группа безопасности сети использовалась для каждой виртуальной подсети. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Без таблицы маршрутов](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Предотвращает развертывание виртуальных сетей с использованием таблицы маршрутов. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |
|**Мониторинг**||
| [Аудит параметров диагностики](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Проверяет, включены ли параметры диагностики для определенных типов ресурсов. Вы можете указать массив типов ресурсов, чтобы проверить, включены ли параметры диагностики. |
|**Соглашение об именовании и тексте**||
| [Разрешение на использование нескольких шаблонов имен](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Разрешение на применение одного из нескольких шаблонов имен для ресурсов. |
| [Требовать шаблон схожести](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что имена ресурсов *соответствуют* условию схожести с шаблоном. |
| [Требовать шаблон совпадения](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что имена ресурсов совпадают с определенным шаблоном именования. |
| [Требовать шаблон совпадения тегов](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что значение тега соответствует текстовому шаблону. |
|**Теги**||
| [Инициатива политики тегов для выставления счетов](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует указать значения тега для обозначения места возникновения затрат и названия продукта. Используются встроенные политики для применения и принудительного назначения требуемых тегов. Нужно указать необходимые значения для тегов.  |
| [Принудительное применение тега и его значения в группе ресурсов](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует указать тег и значение в группе ресурсов. Вы можете указать имя и значение требуемого тега.  |
| [Принудительное применение тега и его значения](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует имя и значение указанного тега. Вы можете указать имя и значение тега для принудительного применения.  |
| [Применение тега и значения по умолчанию](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Добавляет имя и значение указанного тега (если этот тег не предоставлен). Вы можете указать имя и значение применяемого тега.  |
|**Общие сведения**||
| [Допустимые расположения](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы все ресурсы развертывались в допустимых расположениях. Нужно указать массив утвержденных расположений.  |
| [Допустимые типы ресурсов](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Обеспечивает развертывание только допустимых типов ресурсов. Вы можете указать массив допустимых типов ресурсов.  |
| [Недопустимые типы ресурсов](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Запрещает развертывание указанных типов ресурсов. Нужно указать массив типов ресурсов, которые нужно блокировать.  |