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

* Creating an *array* of habits, but what's this **private(set)** we are seeing? 
       
    - We are making a setter variable which means that we can write to our array of habits

    - We are using private because we only want our array of habits to be accessbile through our persistence layer

* This constant is created as a key in User Defaults to store our array of habits

    - User Defaults are used to store basic data types as long as the app is installed. Basic data types include strings, floats, arrays, bools, etc.

    - We are making this constant static because we only want one instance of this key no matter how many times this persistence layer is instantiated

As a developer we are concerned firstly, with what the user is interacting with therefore let is further analyze the loading of habits function for that is what the user will first be seeing, their habits on the screen.

##### Lets add this function to our persistence layer #####
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

* We make this function mutating because we want to alter the copy of data that is assigned when a user instantiates this persistence layer, for more information on this refer to the difference between value and reference types!
    
* Instantiate our user defaults

* Grab our array of habits from our User Defaults for the given key we made earlier

* Decode the json data that it gives us into a swift Habit object, and lastly populate our array of habits with our new Habit object!