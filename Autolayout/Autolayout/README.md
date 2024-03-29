# Autolayout

## Priorities and Content Size

An intrinsic content size is the size a view naturally takes up based on it's content. When views have conflicting constraints they have to fight for space and the only way to decide which view has a higher priority is to set a **Constant Priority**. A value between 0 and 1000 means the view is not required whereas 1000+ means the view is required.

Content Hugging Priorties helps AutoLayout decide what views to stretch past it's intrinsic size, a view with an higher Content Hugging priorities will not want to be stretched/grow and will hug the views bound to the content. The example is shown below.

![horizontal content hugging priority](https://user-images.githubusercontent.com/71823674/196699536-c4f54c90-f080-418a-a119-39a30fa40807.png)

horizontal content hugging priority: green(251)- blue(251)

![horizontal content hugging priority](https://user-images.githubusercontent.com/71823674/196699613-5aaa7f27-cbc9-4a00-8d5f-7b6738832199.png)

horizontal content hugging priority: green(250)- blue(251)

Compression Resistance Priorities helps autolayout decide what views wills shrink smaller than the intrinsic content size. A higher compression resistance will fight harder from being shrunk whilst a lower one will be shrunk.

## StackViews

### Alignment Property

The Alignment propert in stackviews dictate how the views are arranged perpendicular to the stackviews axis.

Horizontal stack view **fill** alignment makes the views take up all of the vertical space inside the stack view as shown below. The vertical space will be decided by the view with the largest height or intrinsic height.

<img width="433" alt="image" src="https://user-images.githubusercontent.com/71823674/196699799-fe077271-1474-4aca-940f-2c19f0da1070.png">

- Top Alignment is set to the top of the Stackview either using their intrinsic height or the height set.

<img width="476" alt="image" src="https://user-images.githubusercontent.com/71823674/196700192-9e53d357-9f7c-4e68-a66c-35c8cbf00be4.png">

- Bottom Alignment means the stackviews subviews are lined up at the bottom

<img width="433" alt="image" src="https://user-images.githubusercontent.com/71823674/196700522-89175e6a-eb0e-4409-b476-a99968a175dd.png">

<img width="434" alt="image" src="https://user-images.githubusercontent.com/71823674/196700776-cf4aa2c3-3238-4673-9c3b-5c9a3e3fbc0b.png">

 Vertical Stack View Alignment looks like this.
 
- **fill** Alignment looks like this
 <img width="153" alt="image" src="https://user-images.githubusercontent.com/71823674/196700909-2a11a284-c651-4f35-a500-daabb06fb7a1.png">
 
 - Leading Alignment looks like this
<img width="150" alt="image" src="https://user-images.githubusercontent.com/71823674/196701004-ab358801-1d29-49b8-82b1-d5f0cb40272a.png">
 
 - Trailing Alignment looks like this
 <img width="136" alt="image" src="https://user-images.githubusercontent.com/71823674/196701065-50a427a2-c96c-4825-b154-9bdf440f2dd9.png">

### Distrubution Property

- **Fill** - this keeps the content as their intrinsic size and only stretches one of them to fill up the space, it determines which ones to stretch by which one has the lowest Content Hugging Priority (CHP).
<img width="295" alt="image" src="https://user-images.githubusercontent.com/71823674/196701262-f5e4bb16-c001-4bad-86c8-9593645c186d.png">

- **Fill Equally** - in this distrubution each of the views are the same size.
<img width="295" alt="image" src="https://user-images.githubusercontent.com/71823674/196701974-ad9afc60-b603-4afe-a373-01da2a20a8d3.png">

- **equalSpacing** - here the stackView gives the arranged subview it's intrisic size then introduces equally-sized padding if there is extra space.

- **equalCentering** - This makes the centre of each view all equal.

## ScrollView

Scrollviews helps you to allow scrolling and zooming of it's contained views and allows you to expand your interfaces beyond the limits of the screen.

In order to use a ScrollView in the code these steps are needed.

``` swift
    //1
    view.addSubview(scrollView)
    
    //2 Set up constraints for that scrollView relative to the mainView
    NSLayoutConstraint.activate([
      scrollView.leadingAnchor.constraint(equalTo:
        view.leadingAnchor),
      scrollView.trailingAnchor.constraint(equalTo:
        view.trailingAnchor),
      scrollView.topAnchor.constraint(equalTo:
        view.safeAreaLayoutGuide.topAnchor),
      scrollView.bottomAnchor.constraint(equalTo:
        view.safeAreaLayoutGuide.bottomAnchor)
    ])
    
    //3 To set up views relative to that scrollView use the contentLayoutGuide variable of the scrollView
    let contentLayoutGuide = scrollView.contentLayoutGuide
    
    NSLayoutConstraint.activate([
      mainStackView.widthAnchor.constraint(equalTo:
        view.widthAnchor),
      mainStackView.leadingAnchor.constraint(equalTo:
        contentLayoutGuide.leadingAnchor),
      mainStackView.trailingAnchor.constraint(equalTo:
        contentLayoutGuide.trailingAnchor),
      mainStackView.topAnchor.constraint(equalTo:
        contentLayoutGuide.topAnchor),
      mainStackView.bottomAnchor.constraint(equalTo:
        contentLayoutGuide.bottomAnchor)
    ])
    
```

## Collection View

### Why a collection view

- Presenting a list in a grid layout.

- Allows for scrolling horizontally.

- Allows sizing a cell's width and height.

- Allows for inserting, deleting and re-ordering items.


## Layout Guides 

### Layout Margins

The layout margin represents the views margins and you can constrain subViews to the superviews margins instead of the superview itself, the default values for
the margins is ` Default Margins = UIEdgeInsets(top: 8, left: 8, bottom: 8, right: 8) `

To anchor a subView to a views margins you have to get the margins layoutGuide and then create constraints against this layout guide.

``` swift
let margins = view.layoutMarginsGuide
NSLayoutConstraint.activate([
    label.centerYAnchor.constraint(equalTo: view.centerYAnchor),
    label.leadingAnchor.constraint(equalTo: margins.leadingAnchor),
    label.trailingAnchor.constraint(equalTo: margins.trailingAnchor),
    ])
```
![layout-margins](https://user-images.githubusercontent.com/71823674/196702067-0c6fc3e3-b169-4d25-a78b-1c9ad5b0e675.png)

### Readable Content Guide

The readbable content guide represents the area that is recommeneded when putting large amounts of text in a view and it is used similarly to layoutMargins.

``` swift
let guide = view.readbaleContentGuide
NSLayoutConstraint.activate([
    textlabel.leadingAnchor.constraint(equalTo: guide.leadingAnchor),
    textlabel.trailingAnchor.constraint(equalTo: guide.trailingAnchor),
    ])
```

### Layout Guide

A layout guide is a container view that can be used instead of another dummy view to organise elements inside of a view when spacing is all you need. In order to use a layout guide then :

``` swift

//1 - Create an initialiser for the layoutGuide
    let layoutGuide = UILayoutGuide()

//2 - Add the layoutGuide to the subView of the view.
    view.addLayoutGuide(layoutGuide)
    
//3 - Add constraints to the layout guide this is to the margins of the view
    NSLayoutConstraint.activate([
      layoutGuide.topAnchor.constraint(
        equalTo: photoImageView.bottomAnchor, constant: 5),
      layoutGuide.leadingAnchor.constraint(
        equalTo: margins.leadingAnchor),
      layoutGuide.trailingAnchor.constraint(
        equalTo: margins.trailingAnchor),
      layoutGuide.bottomAnchor.constraint(
        equalTo: margins.bottomAnchor)
    ])
    
```






 



