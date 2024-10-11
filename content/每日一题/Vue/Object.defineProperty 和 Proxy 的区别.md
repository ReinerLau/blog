---
noteId: 1727840110374
---
# Object.defineProperty 和 Proxy 的区别
---
- **拦截范围** 
	- **Object.defineProperty**: 作用于单个属性
	- **Proxy**: 作用于整个对象 
- **拦截操作**: 
	- **Object.defineProperty**: 读取和设置
	- **Proxy**: 读取、设置、删除、添加等等
