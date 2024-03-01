[../README.md#Guides](../README.md#guides)

# React 18 & React Native

This page describes how to use React 18 with React Native using the React Native's New Architecture.

> **tl;dr:** The first version of React Native compatible with React 18 is **0.69.0**. In order to use the new features in React 18 including automatic batching, `startTransition`, and `useDeferredValue`, you must migrate your React Native app to the New Architecture.

## React 18 and the React Native New Architecture

React 18 introduces [several new features](https://reactjs.org/blog/2022/03/29/react-v18.html) including:

- Automatic batching
- New Strict Mode behaviors
- New hooks (`useId`, `useSyncExternalStore`)

It also includes new concurrent features:

- `startTransition`
- `useTransition`
- `useDeferredValue`
- Full Suspense support

The concurrent features in React 18 are built on top of the new concurrent rendering engine. Concurrent rendering is a new behind-the-scenes mechanism that enables React to prepare multiple versions of your UI at the same time.

Previous versions of React Native built on the old architecture **cannot** support concurrent rendering or concurrent features. This is because the old architecture relied on mutating the native trees, which doesn’t allow for React to prepare multiple versions of the tree at the same time.

Fortunately, the New Architecture was written bottom-up with concurrent rendering in mind, and is fully compatible with React 18. This means, in order to upgrade to React 18 in your React Native app, your application **needs to use the React Native's New Architecture** including Fabric Native Components and Turbo Native Modules.

This means you’re able to use the new features in React 18 as soon as flip the New Architecture switch. Since the new concurrent features are opt-in by using features like `startTransition` or `Suspense`, we expect React 18 to work out-of-the-box with minimal changes for users who migrate to the New Architecture or create a new app with the New Architecture enabled.

### Note for the Old Architecture users

Apps that are still on the Old Architecture will use React 17 mode even if React 18 is listed as a dependency in the `package.json` file.

---

> [!IMPORTANT]
> This documentation is still experimental and details are subject to changes as we iterate.
> Feel free to share your feedback on this [discussion](https://github.com/reactwg/react-native-new-architecture/discussions/8).
>
> Moreover, it contains **several manual steps**. Please note that this won't be representative of the final developer experience once the New Architecture is stable. We're working on tools, templates and libraries to help you get started fast on the New Architecture, without having to go through the whole setup.
