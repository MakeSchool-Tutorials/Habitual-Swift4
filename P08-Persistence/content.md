---
title: Persisting Habits
slug: persisting-habits
---

# Persistence #

In this tutorial we are going to delve into the world of saving data in your application! **Persistence** is a key component in what allows us to manage state for an application.
I understand how that can be confusing so let us add a picture for context.

<img src="https://hungrygopher.com/wp-content/uploads/2015/07/soy_sauce_packet.jpg" width="200" height="200" />

*Trust me this example is going somewhere*


In alot of ways persistence can be thought of as these soy sauce packets. The reason being is that we can think of persistence as a way for us to be able to store data or in this case soy sauce! I can trust these packets to store the sauce even if I am not using them. The only time that changes is if I open the packet, resulting in losing the contents of the packet. 

In more ways than one, persistence in our application acts the same way! We are trusting the application to store our data even after the user exits out of our application. The only time this changes is if the user deletes the application ... in other words, opening the soy sauce packet.

In our application Habitual, we want to be able to persist habits after the user exits the application or in other words save the user's habits.

# Modifying our Habit struct #

We are going to be using NSUSerDefaults to manage persistence in our app. To be able to save objects of type `Habit` we need to make a few changes in our struct to make it conform to the Encodable & Decodable protocol. We can achieve this by adding the `Codable` type to the struct.

```swift
struct Habit: Codable {
...

enum Images: Int, Codable, CaseIterable {
...

```

# Creating the persistence layer

> [action] To start off lets create a PersistenceLayer.swift file

```
struct PersistenceLayer {
    
    // MARK: - VARS
    
    // Line 1
    private(set) var habits: [Habit] = []
    
    // Line 2
    private static let userDefaultsHabitsKeyValue = "HABITS_ARRAY"
    
    init() {
        // Line 3
        self.loadHabits()
    }
}
```

Introducing our first code snippet, lets analyze what is happening!

Creating an *array* of habits, but what's this **private(set)** we are seeing?     
    - We are making a setter variable which means that we can write to our array of habits
    - We are using private because we only want our array of habits to be accessbile through our persistence layer

This constant is created as a key in User Defaults to store our array of habits
    - User Defaults are used to store basic data types as long as the app is installed. Basic data types include strings, floats, arrays, bools, etc.
    - We are making this constant static because we only want one instance of this key no matter how many times this persistence layer is instantiated

As a developer we are concerned firstly, with what the user is interacting with therefore let us further analyze the loading of habits function for that is what the user will first be seeing, their habits on the screen.

> [action] Lets add this function to our persistence layer.

```
    //load

    // Line 1
    private mutating func loadHabits() {

        // Line 2
        let userDefaults = UserDefaults.standard

        // Line 3
        guard
            let habitData = userDefaults.data(forKey: PersistenceLayer.userDefaultsHabitsKeyValue),
            let habits = try? JSONDecoder().decode([Habit].self, from: habitData) else {
                return
        }
        
        self.habits = habits
    }
```

We make this function mutating because we want to alter the copy of data that is assigned when a user instantiates this persistence layer, for more information on this refer to the difference between value and reference types!

Instantiate our user defaults

Grab our array of habits from our User Defaults for the given key we made earlier

Decode the json data that it gives us into a swift Habit object, and lastly populate our array of habits with our new Habit object!

Now that the user is able to load a collection of their habits, they have to have a way to create a habit and persist it!

# Creating habits
> [action] Let's create a createNewHabit function.

```
 // Line 1
 @discardableResult
    
    // Line 2
    mutating func createNewHabit(name: String, image: Habit.Images) -> Habit {

        //Line 3
        let newHabit = Habit(title: name, image: image)
        self.habits.insert(newHabit, at: 0) // Prepend the habits to the array
        self.saveHabits()
        
        return newHabit
    }
```

What is this weird '@discardableResult' we are seeing? Well we add this decorator to this function because we are not going to be using the result of this function directly, if not the compiler will generate a warning for us!
 
    - We create a new habit with the attributes that the user passes in
    - We are prepending our habit to our habit array as denoted by inserting at index 0
    - We then save our new habit and return that new habit

Phewwww, we've been at it awhile, make sure you take some time to stretch.

Now that the user is able to load and create their habits we have to find a way to **save** those changes!

# Saving habits

> [action] Lets add this function to our persistence layer

```
  //save
    private func saveHabits() {
        // Line 1
        guard let habitsData = try? JSONEncoder().encode(self.habits) else {
            fatalError("could not encode list of habits")
        }
        
        // Line 2
        let userDefaults = UserDefaults.standard
        userDefaults.set(habitsData, forKey: PersistenceLayer.userDefaultsHabitsKeyValue)
    }
```

We handle our decoding logic in a guard statement providing us with an early exit if we can't decode our array of habits
    - If we can not decode our array of habits the app will crash with the error statement provided
If we do successfully decode our array of habits we then set that json data inside our User Defaults for htis given key!

Good work ya'll, let's keep grinding! We are able to load, create, and save our habits, but what if the user wanted to **delete** a habit from User Defaults? Resulting in that habit being immediately deleted and not persisting to the next lifetime of the application.

# Deleting habits
> [action] Let's create a delete habit function!

```
 // delete habit
    mutating func delete(_ habitIndex: Int) {
        // Remove habit at the given index
        self.habits.remove(at: habitIndex)

        // Persist the changes we made to our habits array
        self.saveHabits()
    }
```

Only a few more functions we need to add to make this persistence layer worth it's salt. We know that a user has the ability to complete a habit and increase their current streak, the question is how we persist that data and implement that logic. 

# Complete a habit

> [action] Let's add this markHabitAsCompleted function

```
 // Mark Habit Complete

    // Line 1 
    mutating func markHabitAsCompleted(_ habitIndex: Int) -> Habit {

        // Line 2
        var updatedHabit = self.habits[habitIndex] 

        // Line 3
        guard updatedHabit.hasCompletedForToday == false else { return updatedHabit }
        
        // Line 4
        updatedHabit.numberOfCompletions += 1
        
        // Line 5
        if let lastCompletionDate = updatedHabit.lastCompletionDate, lastCompletionDate.isYesterday {
            updatedHabit.currentStreak += 1
        } else {
            updatedHabit.currentStreak = 1
        }
        
        // Line 6
        if updatedHabit.currentStreak > updatedHabit.bestStreak {
            updatedHabit.bestStreak = updatedHabit.currentStreak
        }
        
        // Line 7
        let now = Date()
        updatedHabit.lastCompletionDate = now
        
        // Line 8
        self.habits[habitIndex] = updatedHabit
        self.saveHabits()
        
        return updatedHabit
    }
```

We creare a variable called updatedHabit that stores the habbit at the given index


The next step is checking if that habit has been completed for the day
    
   - If the habit has not been completed then we increment the number of completions by 1
   - If it has already been completed then we return out of this function with the same habit

Line 5 can be a little difficult to read so let's read out it step by step
    
   - We create a constant that is going to store the value of the current habit's last completion date
   - With that value we check if that date was yesterday   
   - If so then we increment the streak of the habit by 1
   - If the it wasn't completed yesterday we set the current streak to 1 denoting either it's a new habit or the user lost their streak on the habit

We then check if the current streak of our chosen habit is better than that habit's best streak!

It's important to update the completion date of the habit so that our previous logic is still accurate at a later time!

We then change the chosen habit to reflect the updated habit with changes we made

Lastly we save our changes made to our habits array and return the newly updated habit

# Swap habits
We made it, with the help of two more functions our persistence layer will be good to go! If the user wanted the ability to swap habits from the order they came to reflect importance, we should give them that ability.

```
// Line 1
 mutating func swapHabits(habitIndex: Int, destinationIndex: Int) {
        let habitToSwap = self.habits[habitIndex]
        self.habits.remove(at: habitIndex)
        self.habits.insert(habitToSwap, at: destinationIndex)
        self.saveHabits()
    }
    
    // Line 2
    mutating func setNeedsToReloadHabits() {
        self.loadHabits()
    }
```

The first function takes to parameters a habitIndex and a destinationIndex representing the two indices of the habits you want to swap
    - We remove the current habit from it's postion and insert it at the destination index
    - We then save the newly made changes to our habit array

The next function comes in handy after we added a new habit and update the collection of habits present in the table view!

# Integrating the persistence layer

Great job folks! Pat yourself on the shoulder and let's move towards the next step! Lets go back to `HabitsTableViewController` and create an isntance of our persistence layer

```swift
    private var persistance = PersistenceLayer()
```

Remember our test data? It's time to get rid of it since we'll be using our array stored in UserDefaults from now on. Let's make some changes to make sure of this.

First lets change the method of the table's data source that says how many rows there will be.

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return persistance.habits.count
}
```

Now, before calling the `configure()` method on each cell. Let's grab the correct habit from the saved array.

```swift
 let habit = persistance.habits[indexPath.row]
 ```

Whenever we load this view controller, we want to load the habits. We can achieve that by adding the method `viewWillAppear()` and call our reload function from the persistence layer. Don't forget to tell the table to reload its content.

```swift
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        persistance.setNeedsToReloadHabits()
        tableView.reloadData()
    }
```

That's it! We are now using our array of habits that lives in the persistence layer. This information will live as long as the app is installed in the phone.
