nteract menu ipc dispatch specification

* Name: spec:2/nteractMenu
* Editor: Jonathan Frederic <jon.freder@gmail.com>
* See also: https://github.com/nteract/nteract
* State: raw

nteract is a an Electron application which can spawn multiple renderer windows.  A single
application wide menu is rendered for all of the windows to share.  Click events on the
menu are then dispatched to the active window using the Electron ipc library.  Each window
listens for incomming ipc messages.  When an ipc dispatch message is received, the window
dispatches the corresponding action to the redux store.

Actions themselves are functions which generate objects that can be dispatched to the
redux store.  The functions themselves are serializable, instead we serialize their names
and jsonable invokation arguments.  The serialization spec follows:

```ts
{
  name: string,
  args?: any[],
}
```

## Implementations


## References

