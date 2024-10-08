[../README.md#Guides](../README.md#guides)

# Appendix

## I. Terminology

The whole New Architecture related guides will stick to the following **terminology**:

- **Legacy Native Components** - To refer to Components which are running on the old React Native architecture.
- **Legacy Native Modules** - To refer to Modules which are running on the old React Native architecture.
- **Fabric Native Components** - To refer to Components which have been adapted to work well with the New Architecture, namely the new renderer. For brevity you might find them referred as **Fabric Components**.
- **Turbo Native Modules** - To refer to Modules which have been adapted to work well with the New Architecture, namely the new Native Module System. For brevity you might find them referred as **Turbo Modules**

## II. Flow Type to Native Type Mapping

You may use the following table as a reference for which types are supported and what they map to in each platform:

### `string`
|   |   |
|---|---|
| Nullable Support? | `?string` |
| Android (Java) | `string` |
| iOS | `NSString` |

### `boolean`
|   |   |
|---|---|
| Nullable Support? | `?boolean` |
| Android (Java) | `Boolean` |
| iOS | `NSNumber` |

### Object literal

This is recommended over using plain `Object`, for type safety.

**Example:** `{| foo: string, ... |}`
|   |   |
|---|---|
| Nullable Support? | <code>?{&#124; foo: string, ...&#124;}</code> |
| Android (Java) | - |
| iOS | - |

### `Object`

> [!NOTE]
> Recommended to use [Object literal](#object-literal) instead.

|   |   |
|---|---|
| Nullable Support? | `?Object` |
| Android (Java) | `ReadableMap` |
| iOS | `@` (untyped dictionary) |

### `Array<*>`
|   |   |
|---|---|
| Nullable Support? | `?Array<*>` |
| Android (Java) | `ReadableArray` |
| iOS | `NSArray` (or `RCTConvertVecToArray` when used inside objects) |

### `Function`
|   |   |
|---|---|
| Nullable Support? | `?Function` |
| Android (Java) | - |
| iOS | - |

### `Promise<*>`
|   |   |
|---|---|
| Nullable Support? | `?Promise<*>` |
| Android (Java) | `com.facebook.react.bridge.Promise` |
| iOS | `RCTPromiseResolve` and `RCTPromiseRejectBlock` |

### Type Unions

Type unions are only supported as callbacks.

**Example:** `'SUCCESS' | 'FAIL'`
|   |   |
|---|---|
| Nullable Support? | Only as callbacks |
| Android (Java) | - |
| iOS | - |

### Callbacks

Callback functions are not type checked, and are generalized as `Object`s.

**Example:** `() =>`
|   |   |
|---|---|
| Nullable Support? | Yes |
| Android (Java) | `com.facebook.react.bridge.Callback` |
| iOS | `RCTResponseSenderBlock` |

> [!Tip]
> You may also find it useful to refer to the JavaScript specifications for the core modules in React Native. These are located inside the `Libraries/` directory in the React Native repository.

## III. TypeScript to Native Type Mapping

You may use the following table as a reference for which types are supported and what they map to in each platform:

### `string`
|   |   |
|---|---|
| Nullable Support? | <code>string &#124; null</code> |
| Android (Java) | `String` |
| iOS | `NSString` |

### `boolean`
|   |   |
|---|---|
| Nullable Support? | <code>boolean &#124; null</code> |
| Android (Java) | `Boolean` |
| iOS | `NSNumber` |

### `number`
|   |   |
|---|---|
| Nullable Support? | No |
| Android (Java) | `double` |
| iOS | `NSNumber` |

### Object literal

This is recommended over using plain `Object`, for type safety.

**Example:** `{| foo: string, ... |}`
|   |   |
|---|---|
| Nullable Support? | <code>?{&#124; foo: string, ...&#124;} &#124; null</code> |
| Android (Java) | - |
| iOS | - |

### `Object`

> [!NOTE]
> Recommended to use [Object literal](#object-literal-1) instead.

|   |   |
|---|---|
| Nullable Support? | <code>Object &#124; null</code> |
| Android (Java) | `ReadableMap` |
| iOS | `@` (untyped dictionary) |

### `Array<*>`
|   |   |
|---|---|
| Nullable Support? | <code>Array<*> &#124; null</code> |
| Android (Java) | `ReadableArray` |
| iOS | `NSArray` (or `RCTConvertVecToArray` when used inside objects) |

### `Function`
|   |   |
|---|---|
| Nullable Support? | <code>Function &#124; null</code> |
| Android (Java) | - |
| iOS | - |

### `Promise<*>`
|   |   |
|---|---|
| Nullable Support? | <code>Promise<*> &#124; null</code> |
| Android (Java) | `com.facebook.react.bridge.Promise` |
| iOS | `RCTPromiseResolve` and `RCTPromiseRejectBlock` |

### Type Unions

Type unions are only supported as callbacks.

**Example:** `'SUCCESS' | 'FAIL'`
|   |   |
|---|---|
| Nullable Support? | Only as callbacks |
| Android (Java) | - |
| iOS | - |

### Callbacks

Callback functions are not type checked, and are generalized as `Object`s.

**Example:** `() =>`
|   |   |
|---|---|
| Nullable Support? | Yes |
| Android (Java) | `com.facebook.react.bridge.Callback` |
| iOS | `RCTResponseSenderBlock` |

> [!Tip]
> You may also find it useful to refer to the JavaScript specifications for the core modules in React Native. These are located inside the `Libraries/` directory in the React Native repository.

## IV. Invoking the code-gen during development

The Codegen is typically invoked at build time, but you may find it useful to generate your native interface code on demand for troubleshooting.

If you wish to invoke the Codegen manually, you have three options:

1. Invoking a Gradle task directly (Android).
2. Invoking a script manually.
3. Use the Codegen CLI.

### Using the CLI

For the simplest and most common use cases you can use the Codegen CLI. Call the following command in your project directory:

```sh
npx react-native codegen
```

This will produce Codegen artefacts in their default locations. You can provide additional options to customize input and output paths, as well as the target platform.

See full description in [The Codegen CLI](./codegen.md#the-codegen-cli) section.

### Android - Invoking a Gradle task directly

You can trigger the Codegen by invoking the following task:

```bash
./gradlew generateCodegenArtifactsFromSchema --rerun-tasks
```

The extra `--rerun-tasks` flag is added to make sure Gradle is ignoring the `UP-TO-DATE` checks for this task. You should not need it during normal development.

The `generateCodegenArtifactsFromSchema` task normally runs before the `preBuild` task, so you should not need to invoke it manually, but it will be triggered before your builds.

### Invoking the script manually

Alternatively, you can invoke the Codegen directly, bypassing the Gradle Plugin or CocoaPods infrastructure.
This can be done with the following commands.

The parameters to provide will look quite familiar to you now that you have already configured the Gradle plugin or CocoaPods library.

#### Generating the schema file

First, you’ll need to generate a schema file from your JavaScript sources. You only need to do this whenever your JavaScript specs change. The script to generate this schema is provided as part of the `react-native-codegen` package. If running this from within your React Native application, you can use the package from `node_modules` directly:

```bash
node node_modules/react-native-codegen/lib/cli/combine/combine-js-to-schema-cli.js \
  <output_file_schema_json> <javascript_sources_dir>
```

> The source for the `react-native-codegen` is available in the React Native repository, under `packages/react-native-codegen`. Run `yarn install` and `yarn build` in that directory to build your own `react-native-codegen` package from source. In most cases, you will not want to do this as the guide assumes the use of the `react-native-codegen` package version that is associated with the relevant React Native nightly release.

#### Generating the native code artifacts

Once you have a schema file for your native modules or components, you can use a second script to generate the actual native code artifacts for your library. You can use the same schema file generated by the previous script.

```bash
node node_modules/react-native/scripts/generate-specs-cli.js \
  --platform <ios|android> \
  --schemaPath <generated_schema_json_file> \
  --outputDir <output_dir> \
  [--libraryName library_name] \
  [--javaPackageName java_package_name] \
  [--libraryType all(default)|modules|components]
```

> [!Note]
> By default, the output artifacts of the Codegen are written to the build folder and should not be committed. They should be considered only for reference.
> It is also possible to include the codegen output into your library. [Read more](./codegen.md#including-generated-code-into-libraries).

##### Example

The following is a basic example of invoking the Codegen script to generate native iOS interface code for a library that provides native modules. The JavaScript spec sources for this library are located in a `js/` subdirectory, and this library’s native code expects the native interfaces to be available in the `ios` subdirectory.

```bash
# Generate schema - only needs to be done whenever JS specs change
node node_modules/react-native-codegen/lib/cli/combine/combine-js-to-schema-cli.js /tmp/schema.json ./js

# Generate native code artifacts
node node_modules/react-native/scripts/generate-specs-cli.js \
  --platform ios \
  --schemaPath /tmp/schema.json \
  --outputDir ./ios \
  --libraryName MyLibSpecs \
  --libraryType modules
```

In the above example, the code-gen script will generate several files: `MyLibSpecs.h` and `MyLibSpecs-generated.mm`, as well as a handful of `.h` and `.cpp` files, all located in the `ios` directory.

## V. Note on Existing Apps

This guide provides instructions for migrating an application that is based on the default app template that is provided by React Native. If your app has deviated from the template, or you are working with an application that was never based off the template, then the following sections might help.

---

> [!IMPORTANT]
> This documentation is still experimental and details are subject to changes as we iterate.
> Feel free to share your feedback on this [discussion](https://github.com/reactwg/react-native-new-architecture/discussions/8).
>
> Moreover, it contains **several manual steps**. Please note that this won't be representative of the final developer experience once the New Architecture is stable. We're working on tools, templates and libraries to help you get started fast on the New Architecture, without having to go through the whole setup.
