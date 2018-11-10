---
title: Adding a UITableView
slug: adding-a-tableview
---

TODO: add transition from P01

# Using a UITableViewController

Let's first start by renaming the `MainViewController` to say `HabitsTableViewController`:

> [action]
> Rename the `MainViewController` class in the **MainViewController.swift** file (also rename the swift file).
> Rename the xib file to be **HabitsTableViewController.xib**. Open that xib file and select the File's Owner object on the left. Then, open the **Attributes Inspector** and update the class to be `HabitsTableViewController`.
>
> ![xcode file's owner](assets/InterfaceBuilder-Files-Owner.png "InterfaceBuilder-Files-Owner")

The difference between a `UIViewController` and a `UITableViewController` is the `UITableViewController` uses a `UITableView` as its **view**. Whereas a `UIViewController` uses a blank `UIView`.
