---
title: Использование Ansible с Azure
description: Основные сведения об использовании Ansible для автоматизированной подготовки облачных решений, управления конфигурацией и развертывания приложений.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241516"
---
# <a name="using-ansible-with-azure"></a>Использование Ansible с Azure

[Ansible](https://www.ansible.com) — этот продукт с открытым кодом, который автоматизирует подготовку облачных решений, управление конфигурацией и развертывание приложений. С помощью Ansible можно подготовить виртуальные машины, контейнеры и сети, а также готовые облачные инфраструктуры. Кроме того, Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде.

Эта статья описывает некоторые преимущества использования Ansible с Azure.

## <a name="ansible-playbooks"></a>Модули playbook Ansible

[Сборники схем Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) позволяют направлять Ansible для настройки среды. Сборники схем программируются на языке YAML, что делает их удобными для восприятия. В разделе "Учебники" приведено множество примеров использования сборников схем для установки и настройки ресурсов Azure. 

## <a name="ansible-modules"></a>Модули Ansible

Ansible содержит набор [модулей Ansible](https://docs.ansible.com/ansible/latest/modules_by_category.html), которые можно выполнять непосредственно на удаленных узлах или с помощью [сборников схем](https://docs.ansible.com/ansible/latest/playbooks.html). Пользователи могут создавать собственные модули. Модули можно использовать для управления системными ресурсами, например службами, пакетами или файлами, или для выполнения системных команд.

Для взаимодействия со службами Azure в Ansible есть набор [облачных модулей Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Эти модули позволяют создавать и координировать инфраструктуру в Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Миграция существующей рабочей нагрузки в Azure

Если вы определили инфраструктуру с помощью Ansible, то вы можете применить сборник схем приложения, чтобы платформа Azure автоматически масштабировала вашу среду при необходимости. 

## <a name="automate-cloud-native-application-in-azure"></a>Автоматизация нативного облачного приложения в Azure

Ansible позволяет автоматизировать собственные облачные приложения в Azure с помощью таких микрослужб Azure, как [Функции Azure](https://azure.microsoft.com//services/functions/) и [Kubernetes в Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Управление развертываниями с помощью динамического списка

С помощью [динамического списка](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) в Ansible можно извлекать список из ресурсов Azure. Вы можете обозначить тегами существующие развертывания Azure и управлять этими отмеченными развертываниями с помощью Ansible.

## <a name="additional-azure-marketplace-options"></a>Дополнительные параметры Azure Marketplace

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) — это образ Azure Marketplace на основе Red Hat. 

Ansible Tower — это пользовательский веб-интерфейс и панель мониторинга для Ansible, которая обладает следующими возможностями.

* Позволяет определить управление доступом на основе ролей, планирование заданий и управление запасами с помощью графического интерфейса. 
* Включает в себя REST API и интерфейс командной строки, что позволяет интегрировать Tower в существующие инструменты и процессы. 
* Поддерживает вывод результатов выполнения сборников схем в реальном времени. 
* Шифрует учетные данные, например, Azure и ключи SSH. Поэтому вы можете делегировать задачи без предоставления учетных данных.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Таблица версий и модулей Ansible для Azure

Ansible содержит набор модулей для подготовки и настройки ресурсов Azure. Эти ресурсы включают в себя виртуальные машины, масштабируемые наборы, сетевые службы и службы контейнеров. В [матрице Ansible](./ansible-matrix.md) перечислены модули Ansible для Azure и версии Ansible, в которые они входят.

## <a name="next-steps"></a>Дополнительная информация

- [Краткое руководство Развертывание шаблона решения Ansible для Azure в CentOS](./ansible-deploy-solution-template.md)
- [Краткое руководство Настройка виртуальных машин Linux в Azure с помощью Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)