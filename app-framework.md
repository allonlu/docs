# Client

1. init

```javascript
    const client = Comm100API.init();
```

2. Method

  - context
  - get
  - instance
  - do
  - on
  - off
  - set
  - trigger

3. app api

  ```javascript
    client.on('app.registered', () => {} );
    client.on('app.activated', () => {} );
    clinet.on('app.deactivated', () => {} );
    clinet.on('app.instance.created', () => {} );
    client.get('app.instances').then((instances) => {});
  ```