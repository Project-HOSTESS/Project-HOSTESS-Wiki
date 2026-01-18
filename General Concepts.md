# Project HOSTESS General Concepts

[GitHub](https://github.com/Project-HOSTESS/)

1. [Structure](#Structure)
2. [Priority](#Priority)
3. [Cloud](#Cloud)
4. [Automation](#Automation)
5. [Delightful features](#Delightful-Features)
6. [Cross-Platform](#Cross-Platform)



## [Structure](#Structure)

### Worlds

**Worlds** allow you to separate drastically-different parts of your life (like _Home_ and _work_), much like most apps require separate accounts for.

### Groups

A **Group** is a set of Tasklists andor subgroups.

A Group can belong to exactly one World, but can be displayed, changed, and referenced in others for convenience.

### Tasklists

A **Tasklist** is a set of tasks. Obviously.

A Tasklist can belong to exactly one Group, but can be displayed, changed, and referenced in others for convenience.

A Tasklist can be recurring or one-shot. When recurring tasklists recur, their completion state is reset.

### Tasks

A **Task** is a myopic item that must be completed. Tasks can contain subtasks (which are also Tasks).

A Task is considered complete when all its subtasks are also complete.

Tasks can belong to exactly one Tasklist, but can be displayed, changed, and referenced in others for convenience.

A Task can be recurring or one-shot.

- When recurring tasks recur, their completion state is reset.
- The recurring/one-shot property is inherited from the task's list by default, but can be changed.
- One-shot tasks within recurring tasks and tasklists are _not_ reset when the tasklist recurs.
- Recurring tasks within recurring tasks and tasklists share the same recur interval as their parent, and this cannot be changed.



## [Priority](#Priority)

A cascading priority system will allow users to let themselves, others with viewing permission, and the app's automation to understand how to prioritize things.

1. User preferences dictate the default priority (first install starts with Medium).
2. Updating that preference gives the option to cascade the change or leave existing tasks at their current priority.
3. Each group has its own priority. By default this is the same as the user's preference.
4. Each tasklist group has its own priority. By default this is the same as its group's.
5. Each item in a tasklist has its own priority. By default this is the same as the tasklist's.

### [Priority tiers can be customized](#Priority_Customization)

Priorities have arbitrary numbers associated (string-parsed real decimal numbers). Priorities have arbitrary colors associated.

Defaults:

1. Immediate - 1000
2. Very High - 800
3. High - 700
4. Medium - 500
5. Low - 300
6. Very Low - 200
7. On hold - 100
8. Won't happen - 0



## [Cloud](#Cloud)

- All data will be saved to a set of servers. That said, a 3rd-party, personal, or local (on-same-machine) server can be used if you want.
- Data will be encrypted in storage and transmission
- You will be able to share and collaborate on groups or tasklists with others using the same server set. You cannot share or collaborate on worlds; those are for separation of personal mindsets.



## [Automation](#Automation)

> This app will have a strong emphasis on automatability.

### [Other-to-app hierarchy](#Automation_Other-to-app)

1. Platform-specific automation events are caught by a specialized plugin for each platform. This allows many approaches, such as Tasker, Apple Events, network-pushed events, etc. to all automate the app.
2. Plugins will conform to a specific API that describes all the ways the app can be automated.
3. The app's central automation service will listen for events fired by plugins via the API via sockets, and automate the app.

### [App-to-other](#Automation_App-to-other)

1. Internal events that are registered as triggering external events will fire to the central automation service.
2. The automation service will pass these events on to any plugins that are registered as listening for them via a similar API as the one used for other-to-app automation.
3. The plugin will then perform the action it deems necessary upon receiving the event.

### [Preinstalled plugins](#Automation_Preinstalled-plugins)

Out-of-the-box, the app will come with plugins for sending notifications to the OS, receiving location change events, etc.



## [UI](#UI)

The UI hasn't been fleshed-out, but it's absolutely crucial that it is made beautiful, delightful, and intuitive. If not, the complexity of the functionality may be miscommunicated, daunting, or just confusing.

### [Notifications](#UI_Notifications)

Notifications are very important in a task management app. In fact, one might argue they're the _most_ important. This means they _must_ be just right, or this app will fail.

- Always use platform-native notifications; these are what the user expects
- Allow notifications to occur _before_ the due date, up to some arbitrary amount the user can specify (e.g. 1 week before, 5 minutes before, etc.)



## [Delightful features](#Delightful-Features)

Delight is essential to the user enjoying the experience of using the app. Here are a few ideas for how this app can be more delightful:

- Location-based silencing and hiding of tasks
- Tiered sorting (both soft and hard), like "By completion status, then by date due, then by name, then by date made"
- Certain tasks can be "pinned to top" to guarantee they're always in your head. Maximum priority and imminently-due tasks can thus be set to be elevated above these priorities.
- Custom tags can be assigned to groups, tasklists, and tasks. These will be reflected in a Tags view and in search queries
- Completion statuses similar to priority tiers (defaults included, customs supported), but can be localized to a single structure
- Defer until another task anywhere else is completed



## [Cross-Platform](#Cross-Platform)

This will be available on all major platforms, using this stack:

|              |    macOS    |     iOS     |    Windows    |     Linux     | Android  |   Web   |
|-------------:|-------------|-------------|---------------|---------------|----------|---------|
|   UI Platform|    Cocoa    | Cocoa Touch |Swing or JavaFX|Swing or JavaFX| Material |HTML+CSS |
|   UI Language|Kotlin/Native|Kotlin/Native|  Kotlin/JVM   |  Kotlin/JVM   |Kotlin/JVM|Kotlin/JS|
|   Local Logic|Kotlin/Native|Kotlin/Native|  Kotlin/JVM   |  Kotlin/JVM   |Kotlin/JVM|Kotlin/JS|
|   Cloud Logic|  Kotlin/JS  |  Kotlin/JS  |   Kotlin/JS   |   Kotlin/JS   |Kotlin/JS |Kotlin/JS|
|Cloud Platform|   Node.JS   |   Node.JS   |    Node.JS    |    Node.JS    | Node.JS  | Node.JS |
