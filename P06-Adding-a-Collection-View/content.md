---
title: Creating A Habit 
slug: creating-habits-habitual
---
TODO: NEED TO ADD THE NAVIGATION FROM TABLE VIEW HOME TO THE ADDHABBITVC AND PUT IT IN ITS OWN NAV CONTROLLER 


Our app so far looks to have many of the barebones components that we need to atleast show our habits. However, how can we show off our habits without creating them first? Lets do that now!

# Outline

First, when we are going through the process of creating the habit, we will select an image that we want to represent our images

To do this, we will setup a new view controller that will house a collection view and each cell will be an image view that contains a picture of a possible habit. 

Next, we will allow our users to select an image from the collection view of their choosing and that image will be highlighted 

Lastly, once the user is done with selecting their image, we will have a button that will allow them to pick a photo and to transition to the 

Lets get to it!

## Creating the New ViewController

> [action]
> Create a new `UIViewController` with the *XIB file choice* selected, 
> name it *AddHabitViewController* 

This will be the view controller that will have our collection view, now let us add that.

>[action]
> In our interface objects library, search for a `CollectionView` and drag and drop it into your new view controller.

Collection Views might be a new concept to you, but not to worry, you have seen something similiar before. You can think of a collection view as a more flexible way to group a "collection" of items, just like a table view, but there is even more flexibility with collection views. They work just like table views, and in fact, they have to still know which view controller will be its delegate and its datasource just like a table view. 

>[action]
> Before we forget, lets control drag from our collection view to the icon that says *file's Owner' and select just the boxes that say *datasource* and *delegate*

> ![Remove Main](./assets/setCollectionView.png)

Now our collection view knows what is responsible for handling the datasource and the delegate, in this case will be the `AddHabitViewController`. 

To finish off the actual layout on this view controller, let us add a button at the bottom that will be pressed once the user picks an image for their habit. 

>[action]
> In our interface objects library search for a `Button` and drag and drop it to the bottom of our view controller and change its text to *Pick Photo* 

Unfortunately, we have this new view controller, but no way to get to this view controller from our `HabitsTableViewController`, lets go back into our `HabitsTableViewController` and instead of the adding a new habbit in the add button, we are going to add the code that will allow us to navigate to our new `AddHabitViewController`: 
> [action]
> In the `pressAddHabit()` function, create an instance of our new `AddHabitViewController` and also create a `UINavigationController` and put our new `AddHabitViewController` imbedded into our `UINavigationController` and then *present* the `UINavigationController`

```
    @objc func pressAddHabit(_ sender: UIBarButtonItem) {
        let addHabitVc = AddHabitViewController.instantiate()
        let navigationController = UINavigationController(rootViewController: addHabitVc)
        present(navigationController, animated: true, completion: nil)
        
    }
```
## Constraining our CollectionView

Great, now let us constrain our new collection view and button 

>[action]
> Using AutoLayout, constrain the collection view to have the following constraints(The height of the Collection View should be 0.75 proportional to the height of the superview):

> ![Remove Main](./assets/collectionViewConstraints.png)

> Also Contrain the button to have a *height* of  *50* and *trailing and leading spaces* to be *15* and the bottom to be aligned *20* from the bottom

Our resulting `AddhabitViewController` should look like this:

> ![Remove Main](./assets/resultingHabitViewController.png)

## CollectionView Cells

We finally finished the initial setup of our AddHabitViewController. We can now move into some code to allow our collection view to show some stuff. 

In order to be able to show an item in our collection view, we have to first layout our cell that will be displayed as a single "item" in our collection view. This cell will simply just be an ImageView. 

>[action]
> Create a new Cocoa Touch Class and name it `HabitImageCollectionViewCell` and have it subclass `UICollectionViewCell` and also check the box that creates the XIB file for you and press next

>[action]
> In our new xib file, drag an *image view* from the object library and name it *habitImage*. Move it to inside the cell and then contrain the new *image view* to be the size of the cell itself. I know this is alot of stuff to do, but you definately can do this! As a hint, make sure to set your image view's auto layout *constraints to 0 on all sides*.

Our new cell class is also going to need to use a reuse identifier to be set also and our imageView's content mode needs to be set to Aspect Fill : 

>[action]
> Select the *collectionViewCell* and then in the *attributes inspector*, set the *identifier* to *habit image cell*. Then click the *imageView* and then click on the *attributes inspector* and set the *content mode* to *Aspect Fill*  

Now we are on our way to be able to write some code!

>[action]
> Open the `HabitImageCollectionViewCell` and delete the code `awakesFromNib()` as we will not be needing this. Now we will add the following code that will be enough to have our cell fully configured. We will also need you to connect the `habitImage` ImageView as it will need to be referenced by our `setImage` function.

```
class HabitImageCollectionViewCell: UICollectionViewCell {
    
    static let identifier = "habit image cell"
    
    static var nib: UINib {
        return UINib(nibName: String(describing: self), bundle: nil)
    }
    
    func setImage(image: UIImage){
        self.habitImage.image = image
    }
    
    @IBOutlet weak var habitImage: UIImageView!
    
}
```
In this code, we are creating a static variable which allows us to access that property within our `AddHabitViewController`, and also allowing cells nib to be called and used for initialization in the future. The `setImage()` property allows us to be able to set the specific image for the cell that will be called in our datasource methods over in our `AddHabitViewController`.

Enough talk of our `AddHabitViewController`, let us add the code we need to be able to finally display our images in the collection view. 

>[action]
> Head over to the `AddHabitViewController` and lets create an `@IBOutlet` for our collection view and lets name it `collectionView` and to also create an `@IBAction` for our button and we will call it `pickPhotoButtonPressed`

Since we have our outlets and actions all ready, we need to also let the `AddHabitViewController` know that it is going to be the delegate and the datasource, and while we are at it, we need to assign the `collectionView` to register the cells that we just created with the identifier that we just set. 

>[action]
> In `viewDidLoad()` add the following code: 
```
    override func viewDidLoad() {
       collectionView.delegate = self
       collectionView.dataSource = self
       collectionView.register(HabitImageCollectionViewCell.nib,
                                forCellWithReuseIdentifier: HabitImageCollectionViewCell.identifier)
       collectionView.allowsMultipleSelection = false
    }
}
```
Awesome! Before we jumpt right into the collection view methods, we will need to do some final setup of our view controller:

>[action]
> Right below `viewDidLoad()` type in `viewWillAppear` and it should autocomplete the overriden function signature where you will add the following code: 
```
    override func viewDidLoad() {
        ...
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        setupNavBar()    
    }
```
We will have to define a setupNavBar function, but we want this functionality to be in `viewWillAppear` since we want our navbar to be setup everytime our view actually appears, and not just when it is initially loaded in `viewDidLoad()`. Add the following code to the `setupNavBar()` function right below the `viewWillAppear`

```
    func setupNavBar() {
        title = "Select Image"
        
        let cancelButton = UIBarButtonItem(barButtonSystemItem: .cancel, target: self, action: #selector(cancelAddHabit(_:)))
        navigationItem.leftBarButtonItem = cancelButton
        
    }
```
Note the #selector which will mean that whenever our new cancel button is pressed, it will look for a function called `cancelAddHabit()`. Let us add that code right below, and dont forget to add the @obj since the #selector syntax is part of objective C , but we wont go into depth about that right now.

>[action]
> Add the `cancelAddHabit()` function below the `setupNavBar()` function: 
```
    @objc func cancelAddHabit(_ sender: UIBarButtonItem) {
        self.presentingViewController?.dismiss(animated: true, completion: nil)
    }
```

## CollectionView Extension

To keep our code cleaner, we are going to use an `extension` that visually allows us to sepreate the collectionView related methods so that we can easily keep track of what is going on with our collection view, or else things would get pretty hectic. 

Using an `extension` here doesnt provide us with any major functional advantage, and would be the same as implementing the delegates as followed: 
```
class AddHabitViewController: UIViewController,UICollectionViewDataSource, UICollectionViewDelegate, UICollectionViewDelegateFlowLayout {
    ...
}
```

Instead, we are going to use an extension which is providing the same purpose. 

>[action]
> create an `extension` below your  and add the following code: 
```
class AddHabitViewController: UIViewController{
    ...
}

extension AddHabitViewController: UICollectionViewDataSource, UICollectionViewDelegate, UICollectionViewDelegateFlowLayout {

}
```

Xcode should now be yelling at us that our view controller does not conform to the `UICollectionViewDataSource` protocol. We can change this by clocking on the red box and pressing `fix` on the error message and it will give us our code stubs, or just add this code: 

```
extension AddHabitViewController: UICollectionViewDataSource, UICollectionViewDelegate, UICollectionViewDelegateFlowLayout {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        
    }
    
    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        
    }
}
```

We may be encountering more errors with our other missing protocols, but let us ignore that for now. We have a `collectionView(..numberOfItemsInSecion)` function that is telling us that we need to give the collection view the number of items it should be expecting per section. In a collection view can have multiple sections. Lets say you wanted a section that will be designated for possibly for cat photos or also maybe a section for cute dog pictures. In our case though, we will stick to one section and no cat or dog pictures:(. Instead, we will have one section that will display 3 columns and as many rows as we may need depending on the amount of images that are provided. Although we have one section, we want our `collectionView(..numberOfItemsInSecion)` to be as many items as the elements that will be provided to me   Our resulting table view will look like:

> ![Remove Main](./assets/collectionViewResult.png)

The `collectionView(..cellForItemAt)` is an important function that will tell the collection view which item to display at the index path, this will make it really easy to use an array and use `indexpath.row` as an index. Do you have any ideas of what array could be the source of the data we need? 

What about the `Images` that belongs to our Habit model that we created earlier? We can use all the images that we are storing with our models that are getting pulled from our assets and use them to set the image in our cell that we created earlier. Let us create the array at the top of our `AddHabitViewController` and not in our `extension` like this:
```
class AddHabitViewController: UIViewController{
    let habitImages = Habit.Images.allCases

    ...
}
```

 We can also add to our collection view functions to reflect some of the ideas that we just discussed. 

>[action]
> Set the number of items in `collectionView(..numberOfItemsInSecion)` to be the length of our `habitImages` array and within the `collectionView(..cellForItemAt)`create a reusable cell and set the cell's image to be at the `indexPath.row` element in our `habitImages` array. To finish off the thought, we also need to set the amount of sections to only one:
```
...
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return habitImages.count
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        
        let cell = collectionView.dequeueReusableCell(
            withReuseIdentifier: HabitImageCollectionViewCell.identifier,
            for: indexPath
            ) as! HabitImageCollectionViewCell

        cell.setImage(image: habitImages[indexPath.row].image)
        return cell
    }

    func numberOfSections(in collectionView: UICollectionView) -> Int {
        return 1
    }
...
```
Its coming along now! Our collection view knows exactly which element should populate the cell and it will also know how many elements it will need to have as well as the number of sections it needs.

## Cell Formatting 
Our content is all finished for our collection view, but now we need to do some formatting using our `UICollectionViewDelegateFlowLayout`. Our Xcode wont stop yelling at us until we finish this last one off. We are getting close:

>[action]
> add the `collectionView(..minimumLineSpacingForSectionAt:)` and the `collectionView(..sizeForItemAt:)` code to set the minimum spacing to *15* and the *width* and the *height* to be 1/4 the size of the collection view's width: 
 ```
     func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumLineSpacingForSectionAt section: Int) -> CGFloat {
        return 15.0
    }
    
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        let collectionViewWidth = collectionView.bounds.width
        return CGSize(width: collectionViewWidth/4, height: collectionViewWidth/4)
    }
```
In this code, we are setting the height and width in proportion to just the width of the collection view since we both want a square sized item and to also have three columns with the spacing included. Now would be a good time to run the app to see if you have everything correct so far, you should see the collection view populated and look like this: 

> ![Remove Main](./assets/collectionViewResult.png)


## Selecting the images

The sole point of this screen is to be able to *select* an image for our new habit, but we cant even do that yet! We can do this by using a handy method provided to us in the `UICollectionViewDelegate` that we implemented in our extension. Once we add this, a user will be able to select a cell: 

```
   func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        let cell = collectionView.cellForItem(at: indexPath)
        cell?.layer.borderWidth = 2.0
        cell?.layer.borderColor = UIColor.yellow.cgColor
    }
```

Once again we create a cell using the selected item, and we will set the cells border to 2 and color it with a nice and ugly yellow! Now we can select a picture but now we run into another problem. We can select as many images as we want!

>[action]
> How can we only get one to be selected? I challenge you to venture off and discover for yourself how you might be able to do this, and if you are stuck just refer to the information below. 

As it turns out, there is a property that we can set called `.allowsMultipleSelection` which will either allow for the collection view to allow multiple items to be selected or not. We do not want that behavior, so we will set it equal to false in our `viewDidLoad()`: 
```
    override func viewDidLoad() {
        ...

       collectionView.allowsMultipleSelection = false
    }
```
Behind the scenes though, when you select an item in the collection view, and you go to select another one, then you are also going to be *deselecting* the last element, but we dont have any function that will handle *deselecting* the cell, so lets add that:  
```
    func collectionView(_ collectionView: UICollectionView, didDeselectItemAt indexPath: IndexPath) {
        let cell = collectionView.cellForItem(at: indexPath)
        cell?.layer.borderWidth = 0.0
    }
```

Thats it! We are now able to select an image just like we wanted to, but its not too much use to us since we need to use that image in our next view controller that we will create in the next section.

# Summary 
In this section, you learned how to create a collection view and also how to create a custom collection view cell. You drew apon your knowledge of Auto Layout to constrain the layouts that we created, and then followed readability practices with `extensions` to make our code more readable. You also learned how to implement the datasource, delegate and layout protocols for collection views and this allowed us to be able to display all the images of possible habits that we wanted. We made so much progress on this page! Take a break, you deserve it, and come back ready for part two of adding the habits. 






































