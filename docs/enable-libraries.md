[../README.md#Guides](../README.md#guides)

# Enable the New Architecture for Libraries

**The first step for supporting the New Architecture in your library is to ensure that it is compatible with the [Interop Layer](https://github.com/reactwg/react-native-new-architecture/discussions/135)**. The Interop Layer makes it possible to use existing native modules written for the legacy architecture with the New Architecture.

If your library is already fully converted to the TurboModules/Fabric APIs, then you can skip this guide. Otherwise, then **you should ensure compatibiltiy with the Interop Layer before proceeding to convert your library to natively use TurboModules/Fabric.**

Starting with React Native 0.74, the Interop Layer is enabled by default. If your library works with it, then no changes will be needed on the user's side in order to use your library.

> [!IMPORTANT]
> If you have already added a Codegen spec to your library, but the library is not fully converted to TurboModules/Fabric, we recommend that you delete it before proceeding. You can add this back when you are ready to convert your library to natively use TurboModules/Fabric without the Interop Layer.

## Ensuring compatibility with the Interop Layer

The Interop Layer will generally work out of the box with simple libraries, but the more complex your library is, the more likely it is that you will need to make some changes. The following sections will guide you through the process of verifying that your library works with the Interop Layer and fixing common issues that developers have encountered.

> [!NOTE]
> It is not necessary to test your library against the New Architecture with bridgeless _disabled_  — it is expected that from React Native 0.74, developers using the New Architecture will have bridgeless _enabled_.

## 1. Test your library with the New Architecture and the Interop Layer

Follow [the guide for testing your library against the latest version of React Native](https://gist.github.com/cipolleschi/82b7a9561b8861330efabbd3eb08c6f5). Be sure to test all of the functionality of your library to ensure that it works as expected.

## 2. Fix any compatibility issues you encounter

The following is a non-exhaustive list of common issues that developers have encountered when testing their libraries with the Interop Layer. If you encounter any of these issues, follow the provided guidance to fix them.

### [JavaScript] `XYZ  is not a function (it is undefined)`

When accessing a module directly from `NativeModules` in the new architecture some operators won't work due to the fact that TurboModule uses object prototypes now, in order to lazily load methods. So, if you used an operator like the spread operator or a function like `Object.keys` on `NativeModules.YourModule` then you will need to change to use `Object.create`. Learn more in [facebook/react-native#43221](https://github.com/facebook/react-native/issues/43221).

<details>
<summary>Spread operator example</summary>

```js
// Before: spread operator worked, but it will not with interop
export default {
  ...NativeModules.RNCNetInfo,
  get eventEmitter(): NativeEventEmitter {
     ...
  }
}
```

```js
// After: use Object.create instead
Object.create(NativeModules.RNCNetInfo, {
   eventEmitter: {
     get: () => {...},
     enumerable: true,
  },
})
```

</details>

<details>
<summary>Object.keys example</summary>

```js
// Before: Object.keys worked, but it will not with interop
Object.keys(NativeModules.RNCNetInfo).forEach((key) => {
  ...
})
```

```js
// After: use for ... in instead
for (const key in NativeModules.RNCNetInfo) {
  ...
}
```

</details>

### [JavaScript] `global.nativeCallSyncHook` can't be used to detect legacy "Remote Debugging in Chrome" with JSC

`global.nativeCallSyncHook === 'undefined'` is a common way to check if you're running in "Remote Debugging in Chrome" (which is not supported with Hermes, only JSC). This is often used to provide some fallback behavior for sync native functions, because they do not work in the legacy remote debugging environment. Use `"RN$Bridgeless" in global && RN$Bridgeless === true` to determine if you are running in bridgeless. Learn more in [LinusU/react-native-get-random-values#57](https://github.com/LinusU/react-native-get-random-values/pull/57).

It is generally not recommended to fork behavior based on whether bridgeless is enabled — this is an escape hatch that should be used sparingly.


<details>
<summary>Example "isRemoteDebuggingInChrome()" function</summary>

```js
function isRemoteDebuggingInChrome () {
  // Remote debugging in Chrome is not supported in bridgeless
  if ('RN$Bridgeless' in global && RN$Bridgeless === true) {
    return false
  }

  return __DEV__ && typeof global.nativeCallSyncHook === 'undefined'
}
```

</details>

### [iOS/Android] Interop Layer is not compatible with custom ShadowNodes

The Interop Layer doesn't work on either Android or iOS if a legacy view is specifying a custom `ShadowNode`, i.e. in Android by overriding the method `getShadowNodeClass`, `createShadowNodeInstance` etc. Fabric won't call those methods and the widget will most likely be rendered incorrectly (i.e. wrong size, 0 height so unclickable, etc.). You can either work around this by not using custom `ShadowNode` or by converting your library to use TurboModules/Fabric without the Interop Layer.

### [Android] `ThemedContext.getNativeModule()` does not behave the same with Interop Layer

A native view manager used to have a `ThemedContext`, which is a class derived from `ReactContext`. It was common to call `ThemedContext.getNativeModule()` or other methods expected to be inherited from `ReactContext`. However, with the Interop Layer this will call to `ReactContext`'s implementation (the `CatalystInstance` one) but not `BridgelessReactContext`. Accessing the internals to the `CatalystInstance` will cause an exception.

You can solve this by using `ThemedContext.getReactApplicationContext().getNativeModule()`.

### [iOS] Cannot access CallInvoker from "RCTCxxBridge.callInvoker"

In bridgeless mode, React Native does not support a fallback for `jsCallInvoker`, and the `bridge.jsCallInvoker` is `nil`. The app will crash when accessing null pointer. Instead, get `callInvoker` from `getTurboModule:`, e.g. [shopify/react-native-skia#2223](https://github.com/Shopify/react-native-skia/pull/2223).

## 3. Stuck? Get help

Search the ["Libraries" discussion category](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries) for similar issues, and if you can't find a solution, post a discussion with details about the issue you are facing.

## 4. Update library status

Once you have verified that your library works with the Interop Layer, update the status of your library on [reactnative.directory](https://reactnative.directory/) to indicate that it is compatible with the New Architecture. If your library is already listed there, set `"newArchitecture": true` in [react-native-libraries.json](https://github.com/react-native-community/directory/blob/main/react-native-libraries.json), otherwise [add your library to the directory](https://github.com/react-native-community/directory?tab=readme-ov-file#how-do-i-add-a-library).

## Next steps

Once your library is compatible with the Interop Layer, release a new version for your users. You can proceed to [converting your library to natively use TurboModules/Fabric](enable-libraries-turbomodules.md) when convenient for you.