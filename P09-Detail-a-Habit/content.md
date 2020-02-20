---
title: Detailing a Habit
slug: detailing-habit
---

Now that we are able to persist a user's habits, we should give them a way to be able to see a detailed view of their habits.

![Detailed View](./assets/DetailedView.gif)

This is something similar to what the finished detail view will look like!

## Creating the details view controller

First, lets create a .xib and a corresponding .swift file for our `HabitDetailViewController`!

**New => File => Cocoa Touch Class => "Name it HabitDetailedViewController" => Include Xib File**

Before we continue to work with this file, we need a way to go from our main table view to the detail of the habit. To achieve this, we should implement the method `didSelectRowAt` in our `HabitsTableViewController`. Whenever we select a habit from the table, we will create an instance of the `HabitDetailViewController` and send our selected habit and its index for further usage. Finally we call the method `pushViewController` to present the new view controller with a push animation.

```
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {

     let selectedHabit = persistence.habits[indexPath.row]
     let habitDetailVC = HabitDetailViewController.instantiate()
     habitDetailVC.habit = selectedHabit
     habitDetailVC.habitIndex = indexPath.row
     navigationController?.pushViewController(habitDetailVC, animated: true)
}

```

You'll need to add those two properties in HabitDetailViewController.

```
var habit: Habit!
var habitIndex: Int!

```

Let's model the xib as seen in the recording up above. You should be familiar with placing elements in a `UIStackView`.


Once you have the design implemented, let's connect our design elements to the corresponding .swift file!

```
import UIKit

class HabitDetailedViewController: UIViewController {

  var habit: Habit!
  var habitIndex: Int!

    private var persistence = PersistenceLayer()

    @IBOutlet weak var imageViewIcon: UIImageView!
    @IBOutlet weak var labelCurrentStreak: UILabel!
    @IBOutlet weak var labelTotalCompletions: UILabel!
    @IBOutlet weak var labelBestStreak: UILabel!
    @IBOutlet weak var labelStartingDate: UILabel!
    @IBOutlet weak var buttonAction: UIButton!

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
    }
}

```

You'll notice that we are **missing** a UI element, one that hasn't been connected to our code yet.

Let's add this code snippet to where we instantiate our IBActions and IB Outlets!

```
@IBAction func pressActionButton(_ sender: Any) {
   habit = persistence.markHabitAsCompleted(habitIndex)
   updateUI()
}
```

- If you refer back to our persistence layer tutorial page you'll remember making a `markHabitAsCompleted` method. When a user presses the complete button we want add the logic of marking that habit as completed in our persistence layer!

- As developers, we adopt a top down approach with what the user is interacting with down to its implementation. We have called an `updateUI` method that does not exist yet ... let's take a look!

```
  private func updateUI() {
        title = habit.title
        imageViewIcon.image = habit.selectedImage.image
        labelCurrentStreak.text = "\(habit.currentStreak) days"
        labelTotalCompletions.text = String(habit.numberOfCompletions)
        labelBestStreak.text = String(habit.bestStreak)
        labelStartingDate.text = habit.dateCreated.stringValue

        if habit.completedToday {
            buttonAction.setTitle("Completed for Today!", for: .normal)
        } else {
            buttonAction.setTitle("Mark as Completed", for: .normal)
        }
    }
```

Let's attack this from a logical standpoint, the user is interacting with this "complete" button, subsequently calling the `updateUI` method.

First we update the UI elements on screen to accurately reflect the changes made to the habit! As you can see we are changing the text values to match the habits current updated values

Then we check if that habit has been completed today, what would make this logic break?


If you start the application you can see that our detailed view does not display accurate information if we don't update the UI when the view ... ?

Take a look and see if you can figure it out yourself!

```
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    updateUI()
}
```

Now try marking the habit as complete and see how it updates.
