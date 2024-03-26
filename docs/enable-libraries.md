[../README.md#Guides](../README.md#guides)

# Enable the New Architecture for Libraries

**The first step for supporting the New Architecture in your library is to ensure that it is compatible with the [interop layer](https://github.com/reactwg/react-native-new-architecture/discussions/135)**. The interop layer makes it possible to use existing native modules written for the legacy architecture with the New Architecture. **You should do this before proceeding to convert your library to natively use TurboModules/Fabric.** Starting with React Native 0.74, the interop layer is enabled by default and, if your library is compatible, then no changes will be needed on the user's side in order to use your library.

## Using the interop layer

The interop layer will generally work out of the box with simple libraries, but the more complex your library is, the more likely it is that you will need to make some changes. The following sections will guide you through the process of verifying that your library works with the interop layer and fixing common issues that you encounter.

> [!IMPORTANT]
> If you have already added a Codegen spec to your library, but the library is not fully converted to TurboModules/Fabric, we recommend that you delete it before proceeding. You can add this back when you are ready to convert your library to natively use TurboModules/Fabric without the interop layer.

### Test your library

Follow [the guide for testing your library against the latest version of React Native](https://gist.github.com/cipolleschi/82b7a9561b8861330efabbd3eb08c6f5). Be sure to test all of the functionality of your library to ensure that it works as expected.

### Common issues (JavaScript)


- `XYZ  is not a function (it is undefined)`. When accessing a module directly from `NativeModules` in the new architecture some operators won't work due to the fact of turbomodule using object prototypes now to lazily load methods. This includes things like the spread operator and `Object.keys`. You can find more details in https://github.com/facebook/react-native/issues/43221

### Common issues (iOS)

(list them here, along with solutions and links to pull requests)

### Common issues (Android)

(list them here, along with solutions and links to pull requests)

### Get help

Search the ["Libraries" discussion category](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries) for similar issues, and if you can't find a solution, post a discussion with details about the issue you are facing.

### Update library status

(post to the discussion? add some metadata to the pkg.json?)

## Next steps

Once your library is compatible with the interop layer, release a new version for your users. You can proceed to [converting your library to natively use TurboModules/Fabric](enable-libraries-turbomodules.md) when convenient for you.