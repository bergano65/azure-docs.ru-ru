---
title: Обнаружение и изучение данных в ADF с помощью Purview
description: Узнайте, как обнаруживать, изучать данные в фабрике данных Azure с помощью зрения
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: accb9bbf195daa3d25e1aed109e36ef309083385
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805315"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Обнаружение и изучение данных в ADF с помощью Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье вы зарегистрируете учетную запись Azure зрения в фабрике данных. Это подключение позволяет обнаруживать ресурсы Azure зрения и взаимодействовать с ними с помощью возможностей ADF. 

В ADF можно выполнять следующие задачи: 
- использовать поле поиска в верхней части окна для поиска ресурсов Purview на основе ключевых слов; 
- изучать данные на основе метаданных, происхождения и заметок; 
- Подключайте эти данные к фабрике данных со связанными службами или DataSets 

## <a name="prerequisites"></a>Предварительные требования 
- [учетная запись Azure Purview;](../purview/create-catalog-portal.md) 
- [Фабрика данных](./quickstart-create-data-factory-portal.md) 
- [Подключение учетной записи Azure зрения к фабрике данных](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Использование Azure зрения в фабрике данных 

Для использования Azure зрения в фабрике данных требуется доступ к этой учетной записи зрения. Фабрика данных передается через разрешение зрения. Например, если у вас есть роль разрешений пленку, вы сможете изменять метаданные, просканированные Azure зрения. 

### <a name="data-discovery-search-datasets"></a>Обнаружение данных: Поиск по наборам DataSet 

Чтобы обнаружить данные, зарегистрированные и проверенные с помощью Azure зрения, можно использовать панель поиска в верхней части портала фабрики данных. Убедитесь, что вы выбрали Azure зрения для поиска всех данных Организации. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Снимок экрана для выполнения над наборами данных.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Действия, которые можно выполнять над наборами данных с помощью ресурсов фабрики 
Вы можете напрямую создать связанную службу, набор данных или поток данных по данным, которые вы ищете в Azure зрения.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Снимок экрана, показывающий, как можно напрямую создать связанную службу, набор данных или поток данных по данным, которые вы ищете в Azure зрения.":::

##  <a name="nextsteps"></a>Дальнейшие действия 

- [Регистрация и проверка ресурсов фабрики данных Azure в Azure зрения](../purview/register-scan-azure-synapse-analytics.md)
- [Поиск данных в каталоге данных зрения Azure](../purview/how-to-search-catalog.md)