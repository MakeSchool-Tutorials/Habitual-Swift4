---
title: Creating the Habit Model
slug: create-the-habit-model
---

We've populated a table view with a list of names.
Now, let's create a `struct` so we can display a list of Habits.

# Habits struct

> [action]
> Create a new **Swift** file and name it **Habit** and paste the following:
>
```swift
>
import Foundation
>
struct Habit {
>
    var title: String
>
}
```
>

Here we've created a `struct` to hold our structured habit and added a property for the title.
Now, to use this `struct`, we'll have to create some habits in-code.

> [action]
> Replace the **names** array with the following:
>
```swift
>
class HabitsTableViewController: UITableViewController {
>
    var habits: [Habit] = [
        Habit(title: "Go to bed before 10pm"),
        Habit(title: "Drink 8 Glasses of Water"),
        Habit(title: "Commit Today"),
        Habit(title: "Stand up every Hour")
    ]
    ...
}
```
>

Update the two table view methods to populate the table view using the array of habits.

> [solution]
>
```swift
>
class HabitsTableViewController: UITableViewController {
>
  override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
>
    return habits.count
>
  }
>
  override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  >
    var cell: UITableViewCell
  >  
    if let dequeueCell = tableView.dequeueReusableCell(withIdentifier: "cell") {
  >
      cell = dequeueCell
  >
      } else {
  >
      cell = UITableViewCell(style: .default, reuseIdentifier: "cell")
  >
    }
  >
    let habit = habits[indexPath.row]
  >
    cell.textLabel?.text = habit.title
  >
    return cell
  >
  }
  >
}
>
```
>

Before you run the project, comment out the body of the **pressAddHabit** method.
Now, you can run the project and see the habits appear in the table view!

# Adding more Properties to Habit

As for the remaining properties, we'll be adding the following:

- dateCreated
- selectedImage
- currentStreak
- bestStreak
- lastCompletionDate
- numberOfCompletions

Add the missing properties in the `Habit struct`.
Keep in mind what the data types are.
Which property will need to be an Optional?
Which property can be changed? Or, should it be a `let` vs a `var`?

> [solution]
>
```swift
import UIKit
>
struct Habit {
    var title: String
    let dateCreated: Date
    var selectedImage: UIImage
>
    var currentStreak: Int
    var bestStreak: Int
    var lastCompletionDate: Date?
    var numberOfCompletions: Int
>
}
```
>


Add the following file **DateExtensions.swift** and paste the following:

```swift
extension Date {
    var stringValue: String {
        return DateFormatter.localizedString(from: self, dateStyle: .medium, timeStyle: .none)
    }

    var isToday: Bool {
        let calendar = Calendar.current
        return calendar.isDateInToday(self)
    }

    var isYesterday: Bool {
        let calendar = Calendar.current
        return calendar.isDateInYesterday(self)
    }
}
```

> [action]
> In the **Habit.swift**, add the hasCompletedForToday property:
>
```swift
import UIKit
>
struct Habit {
    ...
    var completedToday: Bool {
        return lastCompletionDate?.isToday ?? false
    }
}
```

# Loading Images from the Project

The icons we'll have for each habit will be in-app icons.
Meaning, we'll provide a fixed number of icons.

> [action]
> Download [this](https://github.com/MakeSchool-Tutorials/Habitual-Swift4/blob/master/P04-Modeling-the-Habit/assets/Icons.zip) package of images and add each image to your **Assets.xcassets** file in your Xcode project.
> In **Habit.swift**, add the following enum:

```swift
struct Habit {
    enum Images: Int, CaseIterable {
        case book
        case bulb
        case clock
        case code
        case drop
        case food
        case grow
        case gym
        case heart
        case lotus
        case other
        case pet
        case pill
        case search
        case sleep
        case tooth

        var image: UIImage {
            guard let image = UIImage(named: String(describing: self)) else {
                fatalError("image \(self) not found")
            }

            return image
        }
    }
    ...
}
```

Here we have an `enum` that represents each image we added into out **Assets.xcassets** file.
This will make it easier for us when we want to populate a grid of images.
And for each case, you get the image by using `var image: UIImage`.

`CaseIterable` helps us by giving us a class property that returns all the cases in the enum.
`Habits.Images.allCases` will give us an array of all cases.

The last step is to add a custom initializer and assign some default values to our `Habit` struct.


Add the following properties to the Habit struct:

1. Give default values to the following properties:
   - dateCreated
   - currentStreak
   - bestStreak
   - numberOfCompletions
2. Write an initializer that takes in a `String` for the **title** and an `Habit.Images` for the **selectedImage**
3. Update the **selectedImage: UIImage** to use the `Habits.Images` enum.


> [solution]
>
```swift
struct Habit {
  ...
  var title: String
  let dateCreated: Date = Date()
  var selectedImage: Habit.Images
>
  var currentStreak: Int = 0
  var bestStreak: Int = 0
  var lastCompletionDate: Date?
  var numberOfCompletions: Int = 0
>
  var completedToday: Bool {
      return lastCompletionDate?.isToday ?? false
  }
>
  init(title: String, image: Habit.Images) {
      self.title = title
      self.selectedImage = image
  }
}
```

Before you run the project, update the `HabitsTableViewController`'s habits array to say:

```swift
class HabitsTableViewController: UITableViewController {

    var habits: [Habit] = [
        Habit(title: "Go to bed before 10pm", image: Habit.Images.book),
        Habit(title: "Drink 8 Glasses of Water", image: Habit.Images.book),
        Habit(title: "Commit Today", image: Habit.Images.book),
        Habit(title: "Stand up every Hour", image: Habit.Images.book)
    ]
    ...
}
```

For now, nothing has changed on our table view. But, in the next section we'll build a custom cell to display more information about the habit.
