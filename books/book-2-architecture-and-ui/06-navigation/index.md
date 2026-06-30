# DOC 6 — Navigation

**Purpose:** How screens cooperate to create an application

This document teaches how screens connect and communicate using navigation patterns.

## Concepts

- [x] [Why Navigation Exists](./01-why-navigation-exists.md) — The problem navigation solves and why Android needs a dedicated system
- [x] [Navigation Graph](./02-navigation-graph.md) — The XML/code resource that declares all destinations and actions
- [x] [NavController](./03-nav-controller.md) — The object that actually performs navigation and manages the back stack
- [ ] NavHost — The composable/fragment container that displays the current destination
- [ ] Destinations — The screens and fragments that make up a navigation graph
- [ ] Navigate & Arguments — How to move between destinations and pass data
- [ ] Back Stack — How Android tracks navigation history and what controls it
- [ ] Deep Linking — Navigating directly to a destination from outside the app
- [ ] Safe Args / Type-Safe Navigation — Compile-time safe argument passing between destinations
- [x] [Navigation with Compose](./10-navigation-with-compose.md) — Using the Navigation component in a fully Compose app
- [ ] Nested Navigation Graphs — Breaking a large graph into composable sub-graphs
- [ ] Multi-Module Navigation — Navigation across feature modules without coupling
- [ ] Conditional Navigation (auth gates) — Redirecting users based on state before reaching a destination
- [ ] Navigation + ViewModel Scoping — Scoping a ViewModel to a navigation sub-graph
- [ ] Production Navigation Patterns — Patterns teams use in large real-world apps

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
