# Project HOSTESS General Concepts

This document was originally written in 2018, when this project had somewhat different base assumptions. Starting January 2026, it's being updated to include its current concepts.

The original version of this document was a wishlist of ideas. A lot of those remain in this version, and are slowly being replaced as implementations become more concrete.

[GitHub](https://github.com/Project-HOSTESS/)

1. [Structure](#Structure)
2. [Priority](#Priority)
3. [Cloud](#Cloud)
4. [Automation](#Automation)
5. [Delightful features](#Delightful-Features)
6. [Cross-Platform](#Cross-Platform)



## [Structure](#structure)

### Worlds

**Worlds** allow you to separate drastically-different parts of your life (like _Home_ and _work_), much like most apps require separate accounts for.

### Groups

A **Group** is a set of Tasklists andor subgroups.

A Group can belong to exactly one World, but can be displayed, changed, and referenced in others for convenience.

### Tasklists

A **Tasklist** is a set of tasks. Obviously.

A Tasklist can belong to exactly one Group, but can be displayed, changed, and referenced in others for convenience.

A Tasklist can be recurring or one-shot. When recurring tasklists recur, their completion state is reset.

While a Task can have sub-tasks, this differs from a Tasklist in that the Tasklist itself is not a task, but can have state like tags and filters.

### Tasks

A **Task** is an atomic item that must be completed. Tasks can contain subtasks (which are also Tasks).

A Task is considered complete when all its subtasks are also complete.

Tasks can belong to exactly one Tasklist, but can be displayed, changed, and referenced in others for convenience.

A Task can be recurring or one-shot.

The default behavior of recurring tasks is as follows, but can be changed (e.g. user preferences, hacking, automation, client implementation, etc.):
- When recurring tasks recur, their completion state is reset.
- The recurring/one-shot property is inherited from the task's list by default, but can be changed.
- One-shot tasks within recurring tasks and tasklists are _not_ reset when the tasklist recurs.
- Recurring tasks within recurring tasks and tasklists share the same recur interval as their parent, and this cannot be changed.



## [Priority](#priority)

A cascading priority system will allow users to let themselves, others with viewing permission, and the app's automation to understand how to prioritize things.

1. User preferences dictate the default priority (if unchanged, Medium).
2. Updating that preference gives the option to cascade the change or leave existing tasks at their current priority
3. Each group has its own priority. By default this is the same as the user's preference.
4. Each tasklist group has its own priority. By default this is the same as its group's.
5. Each item in a tasklist has its own priority. By default this is the same as the tasklist's.

### [Priority tiers can be customized](#priority-tiers-can-be-customized)

Priorities have arbitrary numbers associated (string-parsed real decimal numbers). Priorities have arbitrary colors associated.

Defaults:

1. Immediate - 1000
2. Very High - 800
3. High - 700
4. Medium - 500 _(default)_
5. Low - 300
6. Very Low - 200
7. On hold - 100
8. Won't happen - 0



## [Cloud](#cloud)

Sync using whatever you want. It's just JSON files in folders.

We are working on a sync system based on Git, but that won't be completed for awhile.



## [Automation](#automation)

> This platform has a strong emphasis on automatability.

### [Other-to-app hierarchy](#other-to-app-hierarchy)

1. Platform-specific automation events are caught by a specialized plugin for each platform. This allows many approaches, such as Tasker, Shortcuts, Automate, Power Tools, network-pushed events, etc. to all automate the app.
2. Plugins conform to a specific API (to be determined) that describes all the ways the app can be automated. Direct manipulation of the data is strongly discouraged for many reasons, including race conditions)
3. The app's central automation service will listen for incoming events via its API (e.g. using networking/sockets), which it will then use to perform the automation (or not, if denied for, e.g., permissions)

### [App-to-other](#app-to-other)

1. Internal events that are registered as triggering automation events, do so by notifying the central automation service.
2. The automation service will pass these events on to any plugins that are registered as listening for them via a similar API (TBD) as the one used for other-to-app automation.
3. The plugin will then perform any action it wants upon receiving the event.



## [UI](#ui)

UI implementers are _strongly_ encouraged to made beautiful, delightful, and intuitive user experiences. If not, the complexity of the functionality may be miscommunicated, daunting, overly-limiting, or just confusing.

### [Notifications](#notifications)

Notifications are very important in a task management app. In fact, one might argue they're the _most_ important. This means they _must_ be just right, or this app will fail.

- Always use platform-native notifications; these are what the user expects. Banners on desktop/phone OSs, login messages in terminals, toasts in webpages, etc.
- Allow notifications to occur _before_ the due date, up to some arbitrary amount the user can specify (e.g. 1 week before, 5 minutes before, etc.)

Notifications will be implemented as automation plugins.



## [Delightful features](#delightful-features)

Delight is essential to the user enjoying the experience of using an app, and improves their learning/intuition of the app's functions. Here are a few ideas for how a HOSTESS app can be more delightful:

- Location-based notifications, and silencing & hiding of tasks
- Tiered sorting (both soft and hard), like "By completion status, then by date due, then by name, then by date made"
- Certain tasks can be "pinned to top" to guarantee they're always in your head.
	- Maximum priority and imminently-due tasks can thus be set to be elevated above pinned tasks.
- Custom tags can be assigned to HOSTESS items (worlds, groups, tasklists, tasks...). These should be reflected in a Tags view and in search queries
<!-- - Completion statuses similar to priority tiers (defaults included, customs supported), but can be localized to a single structure -- what does this mean???-->
- Defer until some other task somewhere else is completed



## [Cross-Platform](#cross-platform)

This will be available on all major platforms, using this stack:

|              | macOS |  iOS  |Windows|Linux| Android  |   Web   |
|-------------:|-------|-------|-------|-----|----------|---------|
|   UI Platform|SwiftUI|SwiftUI|  TBD  | TBD | Compose  |   TBD   |
|   UI Language| Swift | Swift | Swift |Swift|Kotlin/JVM|Kotlin/JS|
|   Local Logic| Swift | Swift | Swift |Swift|Kotlin/JVM|Kotlin/JS|
|   Cloud Logic|  TBD  |  TBD  |  TBD  | TBD |   TBD    |   TBD   |
|Cloud Platform|  TBD  |  TBD  |  TBD  | TBD |   TBD    |   TBD   |
