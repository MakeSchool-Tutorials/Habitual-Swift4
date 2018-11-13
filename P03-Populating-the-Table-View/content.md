---
title: Populating a Table View
slug: populate-a-table-view
---

We have an empty table, it's time to get some content showing up.

# Populating the Table View

A little about the `UITableViewController`, by default this class conforms to the necessary data source methods. These methods are required to populate a `UITableView`. Here are the two required methods:

```swift
// return the number of rows for the given section
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int

// return the UITableViewCell for the given indexPath
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
```

By default, `UITableViewController` implements these methods and returns 0 for the first method and a blank cell for the second method. We'll have to override these methods to provide our own implementation.

> [action]
> Add the following to **HabitsTableViewController.swift** file:

```
class HabitsTableViewController: UITableViewController {
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 5
    }
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        var cell: UITableViewCell
        if let dequeueCell = tableView.dequeueReusableCell(withIdentifier: "cell") {
            cell = dequeueCell
        } else {
            cell = UITableViewCell(style: .default, reuseIdentifier: "cell")
        }
        cell.textLabel?.text = "Hello, world!"
        return cell
    }
}
```

A few things here, we need to add `override` to both of these methods because each method is already implemented in our **superclass**, `UITableViewController`.
Thus, if we want to *override* our parent's implementation, we have to add `override` in front of each method.

## Table View Number of Rows in Section

Let's check out the first method.
The purpose of this is the table view is asking "How many rows does this section have?".

This method gives us two parameters; tableView and section.
The **tableView** is the `UITableView` that is asking for how many rows.
And the **section** is the current section the table view needs to display.
So, we'll have to return an `Int`. This will represent the number of rows for the given `section`.
For now, we'll return **5**.

## Table View Cell at IndexPath

This one is asking a different question; "What does the cell look like at this section and row?".

Again, let's look at the parameters and what we'll have to return.
In the first method, it gave us a **tableView** and **section** value.
This one gives us the same **tableView** but instead of the section number, an **indexPath** is given.

### What is `IndexPath`?

This is a class used by `UITableView` to identify what cell we're referring to.
An `IndexPath` contains two properties; **section** and **row** both are `Int`s.

Now, back to the second method.
This gives us an `IndexPath` which depends on what we returned in the first method.
If in the first method returned 5, the second method will ask for 5 different cells.
This will make more sense once we run a few examples.

In our implementation for this method, we create a new cell by using `UITableViewCell(style:reuseIdentifier:)` and update the cell's textLabel to say *Hello, World!*.
`UITableViewCell` has four styles we could use.
Later, we'll create our own custom cell.

Let's run our project!
We should see five rows that say *Hello, World!*.
Cool!

# Populating a Table View from an Array

This will help us aim closer to making our table view dynamic.
Let's add a property to our `HabitsTableViewController`; we'll be using this new property to populate the table view:

> [action]
> Add the following to **HabitsTableViewController.swift**:
>

```
class HabitsTableViewController: UITableViewController {
    var names: [String] = ["Alan", "Adriana", "Adam", "Anne", "Mitchell", "Dani"]
    ...
}
```

> [challenge]
> Update the two table view methods to populate the table view to show the Strings from the names array.
>

> [solution]
```swift
class HabitsTableViewController: UITableViewController {
>
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return names.count
    }
>
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        var cell: UITableViewCell
        if let dequeueCell = tableView.dequeueReusableCell(withIdentifier: "cell") {
            cell = dequeueCell
        } else {
            cell = UITableViewCell(style: .default, reuseIdentifier: "cell")
        }
>
        let name = names[indexPath.row]
        cell.textLabel?.text = name
>
        return cell
    }
}
```
>

We changed the first method to return the same number as however many elements are in the **names** array.
In the second method, we had to use the **indexPath.row** to check what cell we needed to populate.
Then, we'd update the **textLabel.text** to be equal to the string at that **indexPath.row**.

Give it a run and see if the names appear on the table view!

# Adding New Rows to the Table View

Here, we're going to be using one of the methods of our **tableView** to add a new row to the table view by pressing a button.

> [action]
> Add the following to **HabitsTableViewController.swift**
```swift
class HabitsTableViewController: UITableViewController {
    ...
>
    override func viewDidLoad() {
        super.viewDidLoad()
>
        setupNavBar()
    }
}

extension HabitsTableViewController {

    func setupNavBar() {
        title = "Habitual"
>
        let addButton = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(pressAddHabit(_:)))
        navigationItem.rightBarButtonItem = addButton
    }
>
    @objc func pressAddHabit(_ sender: UIBarButtonItem) {
        names.insert("Hello, World!", at: 0)
        let topIndexPath = IndexPath(row: 0, section: 0)
        tableView.insertRows(at: [topIndexPath], with: .automatic)
    }
}
```
>

Here, we add a `UIBarButtonItem` to the right side of the navigationItem and when the user taps on the button, it will invoke **pressAddHabit**.
There, we'll add *Hello, World!* to the front of the names array.
We also use `tableView.insertRows(at:with:)` which will insert a new row at the given `IndexPath`.
In our case, it'll add a new row to the top of the table view.

Run the project and tap the add button a few times.

> [info]
> it's important that you update the dataSource, in our case the names array, BEFORE you use `tableView.insertRows(at:with:)`. Otherwise, you'll get an inconsistency error in your debugger.
> The table view will fact check by invoking the number of rows.
>

This is great! But, let's populate more than just a list of Strings.
