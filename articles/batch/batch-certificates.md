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
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764323"
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

Пакетная служба имеет API сертификата, [AZ batch certificate create](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Дополнительные сведения об использовании Key Vault см. в статье [Обеспечение безопасного доступа к Key Vault с помощью пакетной службы](credential-access-key-vault.md).
