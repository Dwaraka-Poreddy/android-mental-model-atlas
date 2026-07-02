# DOC 4 — State Management

**Purpose:** How State & Events Are Represented

This document teaches how to represent application state using StateFlow, SharedFlow, and other state holders. It explains the difference between state and events, and how to choose the right tool.

## Concepts Included

- [State](./01-state.md)
- [State Holder](./02-state-holder.md)
- [Characteristics Of State](./03-characteristics-of-state.md)
- [State vs Event](./04-state-vs-event.md)
- [Why StateFlow Exists](./05-why-stateFlow-exists.md)
- [StateFlow](./06-stateFlow.md)
- [MutableStateFlow](./07-mutableStateFlow.md)
- [StateFlow vs Flow](./08-stateFlow-vs-flow.md)
- [SharedFlow](./09-sharedFlow.md)
- [MutableSharedFlow](./10-mutableSharedFlow.md)
- [Replay](./11-replay.md)
- [Channel](./12-channel.md)
- [stateIn](./13-stateIn.md)
- [initialValue](./14-initialValue.md)
- [SharingStarted](./15-sharingStarted.md)
- [Eagerly](./16-eagerly.md)
- [Lazily](./17-lazily.md)
- [WhileSubscribed](./18-whileSubscribed.md)

## Reading Order

Concepts should be read in the order listed because:

1. **What is state?** (State, Characteristics, vs Event)
2. **How do you hold state?** (StateFlow, MutableStateFlow)
3. **How is it different from Flow?** (StateFlow vs Flow)
4. **When do you use alternatives?** (SharedFlow, Channel)
5. **How do you manage sharing?** (stateIn, SharingStarted)

## How This Fits Into The Atlas

This document answers: **How do we represent and manage state in Android applications?**

StateFlow becomes the foundation for screen state (ViewModels in DOC 5) and UI state (Compose in later books).

The next document, **DOC 5 — Android Lifecycle & Architecture**, teaches how to use state holders in actual Android architecture.

---

**Start with:** [State](./01-state.md)
