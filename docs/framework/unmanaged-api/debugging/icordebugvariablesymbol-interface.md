---
title: ICorDebugVariableSymbol 接口
ms.date: 03/30/2017
ms.assetid: 0e58b85e-69bd-41ff-bedb-8cdc8be6a7a2
ms.openlocfilehash: 9d17bc92dcae9e906dfe18d7665fbf489d278234
ms.sourcegitcommit: 13e79efdbd589cad6b1de634f5d6b1262b12ab01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76790863"
---
# <a name="icordebugvariablesymbol-interface"></a>ICorDebugVariableSymbol 接口
检索变量的调试符号信息。  
  
## <a name="methods"></a>方法  
  
|方法|描述|  
|------------|-----------------|  
|[GetName 方法](icordebugvariablesymbol-getname-method.md)|获取变量名。|  
|[GetSize 方法](icordebugvariablesymbol-getsize-method.md)|获取变量的大小（以字节为单位）。|  
|[GetSlotIndex 方法](icordebugvariablesymbol-getslotindex-method.md)|获取本地变量的托管槽索引。|  
|[GetValue 方法](icordebugvariablesymbol-getvalue-method.md)|获取作为字节数组的变量的值。|  
|[SetValue 方法](icordebugvariablesymbol-setvalue-method.md)|将字节数组的值分配给变量。|  
  
## <a name="remarks"></a>备注  
  
> [!NOTE]
> 此接口仅适用于 .NET Native。 如果在 .NET Native 外为 ICorDebug 方案实现此接口，则公共语言运行时将忽略此接口。  
  
## <a name="requirements"></a>需求  
 **平台：** 请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **标头**：CorDebug.idl、CorDebug.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：** [!INCLUDE[net_46_native](../../../../includes/net-46-native-md.md)]  
  
## <a name="see-also"></a>另请参阅

- [调试接口](debugging-interfaces.md)
- [调试](index.md)
