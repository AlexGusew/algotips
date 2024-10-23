# Usage of Bitwise Operations in ReactJS

## Introduction
React's internal implementation makes extensive use of bitwise operations and bitmasks for efficient state management and feature flagging. This post explores how and why React employs these low-level operations in its core functionality.

## TL;DR
React uses bitmasks extensively throughout its codebase, primarily because they enable fast and efficient checking of state inclusion and feature flags. Bitwise operations allow React to pack multiple boolean flags into a single integer, reducing memory usage and improving performance.

## Deep Dive into `react-reconciler`

### ReactFiberFlags
The first notable example is the usage of bitmasks to define node types in React Fiber Tree. Within the Fiber implementation, node states like `Incomplete`, `NeedsPropagation`, and others are defined using bitmasks.

Location: `packages/react-reconciler/src/ReactFiberFlags.js`
![React Fiber Flags Implementation](image.png)

These flags are then used to determine whether a subtree needs rerendering. Here's a key example from the reconciliation process:

```javascript
const rootHasEffect =
    (finishedWork.flags &
        (BeforeMutationMask | MutationMask | LayoutMask | PassiveMask)) !==
    NoFlags;
```
Location: `packages/react-reconciler/src/ReactFiberWorkLoop.js`
![Work Loop Implementation](image-1.png)

Let's break down how this code works:
1. First, it combines multiple masks using the `OR` operator (`|`): `BeforeMutationMask | MutationMask | LayoutMask | PassiveMask`
2. Then, it performs a bitwise `AND` (`&`) between `finishedWork.flags` and the combined masks
3. Finally, it checks if the result is not equal to `NoFlags` (which is zero or `0b0`)
4. If the node flags have no common flags with our search masks, the check evaluates to `0 !== 0`, returning `false`

### React Running Modes
React also uses bitmasks to specify its running modes:
![React Running Modes](image-2.png)

React internally supports seven distinct modes:
- NoMode
- ConcurrentMode
- ProfileMode
- DebugTracingMode
- StrictLegacyMode
- StrictEffectsMode
- NoStrictPassiveEffectsMode

To check if a fiber node is in debug mode, React performs a bitwise `AND` operation:
```javascript
sourceFiber.mode & DebugTracingMode
```
![Debug Mode Check](image-3.png)

One elegant aspect is how easily React combines fiber node modes using the bitwise `OR` operator:
![Combining Modes](image-4.png)

### Event Types
React's event system also leverages bitmasks for flag management:
![Event System Flags](image-5.png)

These flags are used in two primary ways:
1. Adding modes using the `OR` operation
2. Checking for specific modes using the `AND` operation:
![Event Type Usage](image-6.png)

Interesting observations:
- Event types use left shift notation (`1 << 2`) instead of explicit binary notation (`0b0000000100000000000000000000`)
- The `IS_PASSIVE` event type exists in the codebase but is currently unused in the React monorepo

## Notable Exception: Scheduler Priorities
Interestingly, React's scheduler priorities don't use bitmasks:
![Scheduler Priorities](image-7.png)

This design choice makes sense because priorities require comparison operations (greater than, less than) rather than set inclusion checks. While it would be possible to implement priorities using bitmasks, simple numeric values are more appropriate for this use case.

## Summary
React's use of bitwise operations demonstrates thoughtful performance optimization. Bitmasks are employed when checking for feature inclusion or state combinations, while simpler numeric values are used for comparative operations like priority scheduling. This selective use of bitwise operations shows how React's internal architecture carefully chooses the right tool for each specific requirement.