---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522822"
---
## <a name="select-an-ingestion-type"></a>Выберите тип приема данных

Для параметра **Ingestion type** (Тип приема данных) вы берите один из таких вариантов:
   * **Из хранилища**: в поле **Link to storage** (Ссылка на хранилище) добавьте URL-адрес учетной записи хранения. Для частных учетных записей хранения используйте [подписанный URL-адрес BLOB-объекта](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).
   
      ![Прием данных из хранилища одним щелчком](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **Из файла**: нажмите кнопку **Обзор**, чтобы найти файл, или перетащите файл в поле.
  
      ![Прием данных из файла одним щелчком](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **from container** (Из контейнера): в поле **Link to storage** (Ссылка на хранилище) добавьте [подписанный URL-адрес](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) контейнера и при необходимости укажите размер выборки.

      ![Прием данных из контейнера одним щелчком](media/data-explorer-one-click-ingestion-types/from-container.png)

  Появится пример данных. При необходимости можно отфильтровать эти данные, чтобы отображались только те файлы, имена которых в начале или в конце имеют определенные символы. Когда вы меняете фильтры, содержимое в окне предварительного просмотра обновляется автоматически.
  
  Например, можно выполнить фильтрацию по всем файлам, имена которых начинаются со слова *data* и заканчиваются расширением *.csv.gz*.

  ![Фильтрация для функции приема данных одним щелчком](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
