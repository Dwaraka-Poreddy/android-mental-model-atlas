# DOC 6 — Navigation

**Purpose:** How screens cooperate to create an application

This document teaches how screens connect and communicate using navigation patterns.

## Concepts

- [x] [Why Navigation Exists](./01-why-navigation-exists.md) — The problem navigation solves and why Android needs a dedicated system
- [x] [Navigation Graph](./02-navigation-graph.md) — The blueprint that defines every destination and how they connect
- [x] [NavController](./03-nav-controller.md) — The component that performs navigation and coordinates navigation state
- [x] [NavHost](./04-nav-host.md) — The container that displays the current destination
- [x] [Navigate & Arguments](./05-navigate-and-arguments.md) — Moving between destinations and passing data
- [x] [Back Stack](./06-back_stack.md) — How Android remembers previously visited destinations
- [x] [Deep Linking](./07-deep_linking.md) — Opening a destination directly from outside the application
- [x] [Type-Safe Navigation](./08-type_safe_navigation.md) — Compile-time safe argument passing
- [x] [Nested Navigation Graphs](./09-nested_navigation_graphs.md) — Organizing large applications into smaller navigation graphs
- [x] [Multi-Module Navigation](./10-multi_module_navigation.md) — Navigating across feature modules without tight coupling
- [x] [Conditional Navigation](./11-conditional_navigation.md) — Redirecting users based on application state
- [x] [ViewModel Scoping](./12-viewmodel_scoping.md) — Scoping ViewModels to navigation destinations and graphs
- [x] [Production Navigation Patterns](./13-production_navigation_patterns.md) — Navigation architecture used in large production applications
- [x] [Navigation with Compose](./14-navigation_with_compose.md) — Implementing the navigation concepts using Jetpack Compose

## Reading Order

Concepts should be read in the order listed because:

1. **Understand why** (Why Navigation Exists)
2. **Learn the core components** (Navigation Graph, NavController, NavHost)
3. **Learn how to use them** (Destinations, Navigate & Arguments, Back Stack)
4. **Learn advanced features** (Deep Linking, Type-Safe Navigation, Compose)
5. **Master production patterns** (Nested graphs, multi-module, conditional navigation)

## How This Fits Into The Atlas

This document answers: **How do screens connect together?**

After learning about architecture and ViewModel in DOC 5, readers need to understand how screens communicate.

The next document, **DOC 7 — Jetpack Compose**, teaches how to implement UIs for these screens.

---

**Start with:** [Why Navigation Exists](./01-why-navigation-exists.md)
