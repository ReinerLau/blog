---
tags:
  - React
---
## 挂载

```ts
function MyComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    // 挂载操作
  }, []); 
}
```

## 更新

```ts
function UserProfile({ userId }) {
  useEffect(() => {
    // 更新操作
  }, [userId]); 
}
```

## 卸载

```ts
function MyComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    return () => {
	    // 卸载操作
    }
  }, []); 
}
```