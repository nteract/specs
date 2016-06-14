This spec describes a mechanism for extending nteract via plugins.

* Name: spec:2/nteractPlugins
* Editor: Jonathan Frederic <jon.freder@gmail.com>
* See also: https://github.com/nteract/nteract
* State: raw

The nature of nteract lends itself to scope creep.  Scope creep decrease the maintainability of the project and increases the bus factor.  It's important that the scope of nteract is well defined and that its maintainers actively resist the growth of scope.  To help mitigate the growth of nteract while not inhibiting it's ability to grow in functionality, a healthy plugin mechanism must exist.  Instead of growing nteract, users can write plugins for nteract that add functionality.

There are two major components in the design of a plugin system for nteract:
1. registry of plugins - how the user installs/uninstalls plugins and how nteract is made aware of them
2. extension points in nteract itself - what programatic points are exposed to the extensions for modification

For the registry, an existing solution can be used or one can be designed from the ground up.  nteract doesn't require anything special, just the ability to install, uninstall, and list plugins.  This suggests an existing solution can be used to reduce man hours and maintance load.  

npm, the package manager used by nteract itself, will be used as a plugin registry.  npm packages prefixed with `nteract-plugin-` will be automatically detected and loaded by nteract.  Users can then install plugins locally via `npm install <packagename>` and uninstall them using `npm uninstall <packagename>`.  For system wide installations `npm install -g <packagename>` can be used to install and `npm uninstall -g <packagename>` to uninstall.

Plugins must export a class.  Attributes of that class will be recognized and handled by name.  The class may implement zero or more of these named attributes.  The following demonstrates what attribute names are available:

```js
export default class Plugin {
  /**
   * Called when the plugin is loaded
   * @param  {(pluginName) => Promise<object>} requirePlugin - make sure that another
   *                          plugin is loaded by the plugin system.  The promise will
   *                          evaluate with a handle on that plugin's instance once it
   *                          has be loaded by the plugin system.
   * @param  {Redux.store} store - handle to the redux store
   */
  constructor(requirePlugin, store) {}

  /**
   * Called when the plugin is unloaded
   * @return {[type]} [description]
   */
  dispose() {}

  /**
   * Dictionary of reducers for the redux store to use
   * @return {object} action names mapped to reducer functions
   */
  get reducers() {}

  /**
   * CSS that should be applied to the window
   * @return {string} CSS content
   */
  get css() {}

  /**
   * Map of theme names to CSS content that should be applied to the window when
   * the theme is active.
   * @return {object} map of theme names to CSS content
   */
  get themes() {}
}
```

nteract will load all of the plugins it finds at page load time.  The loading will
be done "synchronously", in that it will cause the splash screen to remain visible
until all extensions are loaded.  The word "synchronously" is used loosely here
in that the underlying Javascript may be asynchronous, it will just appear
synchronous to the user.  Extension load failures will not cause the application
to fail to load.

## Implementations

N/A

## References

* [nteract](https://github.com/nteract/nteract)
* [comment on npm issue 8283, explaining that npm is only stable via the CLI]( https://github.com/npm/npm/issues/8283#issuecomment-103718763)
