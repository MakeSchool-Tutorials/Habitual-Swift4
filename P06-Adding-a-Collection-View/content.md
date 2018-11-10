---
title: Creating A Habit 
slug: creating-habits-habitual
---
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

Our new cell class is also going to need to use a reuse identifier to be set also: 

>[action]
> Select the *collectionViewCell* and then in the *attributes inspector*, set the *identifier* to *habit image cell*. 

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

We may be encountering more errors with our other missing protocols, but let us ignore that for now. We have a `collectionView(..numberOfItemsInSecion)` function that is telling us that we need to give the collection view the number of items it should be expecting per section. In a collection view can have multiple sections. Lets say you wanted a section that will be designated for possibly for cat photos or also maybe a section for cute dog pictures. In our case though, we will stick to one section and no cat or dog pictures:(. Instead, we will have one section that will display 3 columns and as many rows as we may need depending on the amount of images that are provided.
























