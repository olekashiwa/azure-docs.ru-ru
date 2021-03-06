---
title: включить файл
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 689889588aba4da888a7d66f5e1d45dfde71d520
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021054"
---
1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.

2. В диалоговом окне **Выберите целевой объект публикации** используйте параметры публикации, указанные в таблице под изображением. 

    ![Выбор целевого объекта публикации](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | Параметр      | Description                                |
    | ------------ |  -------------------------------------------------- |
    | **План потребления службы "Функции Azure"** |   При публикации проекта в приложении-функции, которое работает в [плане потребления](../articles/azure-functions/functions-scale.md#consumption-plan), вы платите только за выполнение приложения-функции. Другие планы размещения связаны с дополнительными расходами. Дополнительные сведения см. в статье [Масштабирование и размещение Функций Azure](../articles/azure-functions/functions-scale.md). | 
    | **Создать** | В Azure создается приложение-функция со связанными ресурсами. Если выбрать параметр **Выбрать существующее**, все файлы в существующем приложении-функции в Azure будут перезаписаны файлами из локального проекта. Используйте этот параметр только при повторной публикации обновлений для существующего приложения-функции. |
    | **Запустить из файла пакета** | Приложение-функция развертывается с помощью [Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) с включенным режимом [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) (Выполнение из пакета). Этот способ повышает производительность приложения-функции и является рекомендуемым. Если этот параметр не используется, перед публикацией в Azure остановите локальное выполнение проекта приложения-функции. |


3. Нажмите кнопку **Опубликовать**. Если вы еще не вошли в учетную запись Azure из Visual Studio, выберите **Вход**. Вы также можете создать бесплатную учетную запись Azure.

4. В **Службе приложений Azure: создание** укажите параметры **размещения**, которые приведены в таблице ниже:

    ![Диалоговое окно "Создание службы приложений"](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Параметр      | Рекомендуемое значение  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Название** | Глобально уникальное имя | Имя, которое однозначно идентифицирует новое приложение-функцию. Допустимые символы: `a-z`, `0-9` и `-`. |
    | **подписка** | Выберите свою подписку | Подписка Azure, которую нужно использовать. |
    | **[Группа ресурсов](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Имя группы ресурсов, в которой создается приложение-функция. Чтобы создать группу ресурсов, выберите **Создать**.|
    | **[План размещения](../articles/azure-functions/functions-scale.md)** | План потребления | Для создания бессерверного плана щелкните **Создать**, а затем выберите **Потребление** в разделе **Размер**. Также нужно выбрать **расположение** в ближайшем [регионе](https://azure.microsoft.com/regions/) или в регионе, расположенном рядом с другими службами, к которым обращаются ваши функции. Когда выполняется план, отличный от **Потребление**, необходимо будет управлять [масштабированием приложения-функции](../articles/azure-functions/functions-scale.md).  |
    | **[Служба хранилища Azure](../articles/storage/common/storage-account-create.md)** | Учетная запись хранения общего назначения | Учетная запись хранения Azure — обязательный ресурс для среды выполнения Функций. Выберите **Создать**, чтобы создать учетную запись хранения общего назначения. Можно также использовать существующую учетную запись при условии, что она соответствует [требованиям учетной записи хранилища](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Выберите **Создать**, чтобы создать приложение-функцию и связанные с ним ресурсы в Azure с заданными параметрами, а также развернуть код проекта функции. 

6. По окончании развертывания запомните или запишите **URL-адрес сайта**, который является адресом приложения-функции в Azure.

    ![Сообщение об успешной публикации](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
