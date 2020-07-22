---
title: Использование сертификатов в пакетной службе Azure
description: Использование сертификатов для включения проверки подлинности приложений
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 96a3ada98bb41ea007eaaae2a40983d2448b38c2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960832"
---
# <a name="using-certificates-with-batch"></a>Использование сертификатов с пакетной службой

В настоящее время главной причиной использования сертификатов с пакетной службой является наличие приложений, работающих в пулах, которые должны проходить проверку подлинности с помощью конечной точки. 

Если у вас еще нет сертификата, вы можете создать самозаверяющий сертификат с помощью программы командной строки `makecert`.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Загрузка сертификатов вручную на портале Azure

1. В учетной записи пакетной службы, в которую необходимо загрузить сертификат, выберите **Сертификаты**, а затем щелкните **Добавить**. 

2. Отправьте сертификат с расширением PFX или CER. 

После завершения отправки сертификат появится в списке сертификатов, а вы сможете проверить отпечаток.

Теперь при создании пула пакетной службы вы можете открыть вкладку "Сертификаты" для этого пула и назначить ему только что отправленный сертификат.

## <a name="next-steps"></a>Дальнейшие действия

Пакетная служба имеет API сертификата, [AZ batch certificate create](/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Дополнительные сведения об использовании Key Vault см. в статье [Обеспечение безопасного доступа к Key Vault с помощью пакетной службы](credential-access-key-vault.md).
