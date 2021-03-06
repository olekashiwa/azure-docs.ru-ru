---
title: Защита кластеров Azure обозреватель данных в Azure
description: Узнайте, как защитить кластеры в Azure обозреватель данных.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5f3bceb8398f9837f6f8eaa390def41456daf08d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271594"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Защита кластеров Azure обозреватель данных в Azure

В этой статье содержатся общие сведения о безопасности в Azure обозреватель данных, помогающие защитить данные и ресурсы в облаке и удовлетворить потребности системы безопасности вашего бизнеса. Важно обеспечить безопасность кластеров. Защита кластеров включает в себя одну или несколько функций Azure, которые включают безопасный доступ и хранилище. В этой статье содержатся сведения, помогающие обеспечить безопасность кластера.

## <a name="managed-identities-for-azure-resources"></a>Управляемые удостоверения для ресурсов Azure

Распространенной проблемой при создании облачных приложений является управление учетными данными в коде для проверки подлинности в облачных службах. Обеспечение безопасности учетных данных является важной задачей. Учетные данные не должны храниться на рабочих станциях разработчика или возвращены в систему управления версиями. Azure Key Vault позволяет обеспечить безопасное хранение учетных данных, секретов, а также других ключей, но для их получения код должен выполнять проверку подлинности в Key Vault.

Эта проблема решается в Azure Active Directory (Azure AD) управляемые удостоверения для ресурсов Azure. Функция предоставляет службам Azure автоматически управляемое удостоверение в Azure AD. Удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде. Дополнительные сведения об этой службе см. на странице Обзор [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) .

## <a name="data-encryption"></a>шифрование данных;

### <a name="azure-disk-encryption"></a>Шифрование дисков Azure

[Шифрование дисков Azure](/azure/security/azure-security-disk-encryption-overview) помогает защитить данные в соответствии с обязательствами по обеспечению безопасности и соответствия требованиям Организации. Он обеспечивает шифрование томов для дисков операционной системы и данных виртуальных машин кластера. Шифрование дисков Azure также интегрируется с [Azure Key Vault](/azure/key-vault/), что позволяет управлять ключами и секретами шифрования диска и управлять ими и обеспечивать шифрование всех данных на дисках виртуальной машины. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Ключи, управляемые клиентом, с Azure Key Vault

По умолчанию данные шифруются с помощью ключей, управляемых корпорацией Майкрософт. Для дополнительного управления ключами шифрования можно предоставить ключи, управляемые клиентом, для шифрования данных. Вы можете управлять шифрованием данных на уровне хранилища с помощью собственных ключей. Ключ, управляемый клиентом, используется для защиты и контроля доступа к корневому ключу шифрования, который используется для шифрования и расшифровки всех данных. Ключи, управляемые клиентом, обеспечивают большую гибкость при создании, повороте, отключении и отмене контроля доступа. Кроме того, можно выполнять аудит ключей шифрования, используемых для защиты данных.

Используйте Azure Key Vault для хранения ключей, управляемых клиентом. Вы можете создавать собственные ключи и сохранять их в хранилище ключей, а также использовать Azure Key Vault API для создания ключей. Кластер Azure обозреватель данных и Azure Key Vault должны находиться в одном регионе, но могут находиться в разных подписках. Дополнительные сведения о Azure Key Vault см. в разделе [что такое Azure Key Vault?](/azure/key-vault/key-vault-overview). Подробное описание ключей, управляемых клиентом, см. в разделе [ключи, управляемые клиентом, с помощью Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Управляемые клиентом ключи используют управляемые удостоверения для ресурсов Azure, функции Azure Active Directory (Azure AD). Чтобы настроить ключи, управляемые клиентом, в портал Azure необходимо настроить управляемое удостоверение **SystemAssigned** в кластере.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Хранение ключей, управляемых клиентом, в Azure Key Vault

Чтобы включить управляемые клиентом ключи в кластере, используйте Azure Key Vault для хранения ключей. Необходимо включить как **обратимое удаление** , так и **не очищать** свойства в хранилище ключей. Хранилище ключей должно находиться в той же подписке, что и кластер. Azure обозреватель данных использует управляемые удостоверения для ресурсов Azure для проверки подлинности в хранилище ключей для операций шифрования и расшифровки. Управляемые удостоверения не поддерживают сценарии с несколькими каталогами.

#### <a name="rotate-customer-managed-keys"></a>Смена ключей, управляемых клиентом

Вы можете поворачивать ключ, управляемый клиентом, в Azure Key Vault в соответствии с политиками соответствия требованиям. При вращении ключа необходимо обновить кластер, чтобы он использовал новый универсальный код ресурса (URI) ключа. Вращение ключа не вызывает повторное шифрование данных в кластере. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Отозвать доступ к ключам, управляемым клиентом

Чтобы отозвать доступ к ключам, управляемым клиентом, используйте PowerShell или Azure CLI. Дополнительные сведения см. в разделе [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) или [Azure Key Vault CLI](/cli/azure/keyvault). Отмена доступа блокирует доступ ко всем данным на уровне хранилища кластера, так как ключ шифрования, следовательно, недоступен обозреватель данных Azure.

> [!Note]
> Когда обозреватель данных Azure определяет, что доступ к ключу, управляемому клиентом, отменяется, он автоматически приостанавливает кластер для удаления всех кэшированных данных. После возврата доступа к ключу необходимо возобновить работу кластера вручную.

## <a name="role-based-access-control"></a>Контроль доступа на основе ролей

С помощью [управления доступом на основе ролей (RBAC)](/azure/role-based-access-control/overview)можно разделить обязанности внутри группы и предоставить только необходимый доступ пользователям кластера. Вместо того чтобы предоставлять всем неограниченным разрешениям доступ к кластеру, можно разрешить только определенные действия. Вы можете настроить [Контроль доступа для баз данных](/azure/data-explorer/manage-database-permissions) в [портал Azure](/azure/role-based-access-control/role-assignments-portal)с помощью [Azure CLI](/azure/role-based-access-control/role-assignments-cli)или [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка управляемых удостоверений для кластера Azure обозреватель данных](managed-identities.md)
* [Защитите свой кластер в Azure обозреватель данных-Portal](manage-cluster-security.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Настройка ключей, управляемых клиентом, с помощьюC#](customer-managed-keys-csharp.md)

