# Autolayout

## Priorities and Content Size

An intrinsic content size is the size a view naturally takes up based on it's content. When views have conflicting constraints they have to fight for space and the only way to decide which view has a higher priority is to set a **Constant Priority**. A value between 0 and 1000 means the view is not required whereas 1000+ means the view is required.

Content Hugging Priorties helps AutoLayout decide what views to stretch past it's intrinsic size, a view with an higher Content Hugging priorities will not want to be stretched/grow and will hug the views bound to the content. The example is shown below.

https://miro.medium.com/max/944/1*fvHZ_dT1fEv2gSmL9WYXug.png
horizontal content hugging priority: green(251)- blue(251)

https://miro.medium.com/max/950/1*0COR1RWl5VhrpuCoIQ_MlA.png
horizontal content hugging priority: green(250)- blue(251)

Compression Resistance Priorities helps autolayout decide what views wills shrink smaller than the intrinsic content size. A higher compression resistance will fight harder from being shrunk whilst a lower one will be shrunk.

## StackViews

### Alignment Property

The Alignment propert in stackviews dictate how the views are arranged perpendicular to the stackviews axis.

Horizontal stack view **fill** alignment makes the views take up all of the vertical space inside the stack view as shown below. The vertical space will be decided by the view with the largest height or intrinsic height.
https://assets.alexandria.raywenderlich.com/books/alt/images/a126245a5207673b9428c7a5906c076b015a985198487d6f666c8f4156f85cc5/original.png

- Top Alignment is set to the top of the Stackview either using their intrinsic height or the height set.
https://assets.alexandria.raywenderlich.com/books/alt/images/1b9de05472f2e6e822e20adbf19f0d3f3d2358e9b7758840def9dcd7e01b5c7b/original.png

- Bottom Alignment means the stackviews subviews are lined up at the bottom
https://assets.alexandria.raywenderlich.com/books/alt/images/58df03c52d2ad96040a36ab4141b9e37dc87e73cb8682f2441f0b098093d85f9/original.png

 Vertical Stack View Alignment looks like this.
 
- **fill** Alignment looks like this
 https://assets.alexandria.raywenderlich.com/books/alt/images/08221444e3d202b5497b5cb99bf9bd49160d18763ac5e4a035b0b84f886593f6/original.png
 
 - Leading Alignment looks like this
https://assets.alexandria.raywenderlich.com/books/alt/images/1b01a22097cdaae06ca7a4929f57761ae1c2ab3d695dfde57a707e56d3964f71/original.png
 
 - Trailing Alignment looks like this
 https://assets.alexandria.raywenderlich.com/books/alt/images/f6c3da9d8191778c722538daf66e82cd7b564153a63ec4fabc7c66548b2c82cc/original.png

### Distrubution Property

- **Fill** - this keeps the content as their intrinsic size and only stretches one of them to fill up the space, it determines which ones to stretch by which one has the lowest Content Hugging Priority (CHP).
https://spin.atomicobject.com/wp-content/uploads/20160610170335/uistackview-fill.png

- **Fill Equally** - in this distrubution each of the views are the same size.
https://spin.atomicobject.com/wp-content/uploads/20160610172413/uistackview-fill-equally.png

- **equalSpacing** - here the stackView gives the arranged subview it's intrisic size then introduces equally-sized padding if there is extra space.

- **equalCentering** - This makes the centre of each view all equal.

## ScrollView

Scrollviews helps you to allow scrolling and zooming of it's contained views and allows you to expand your interfaces beyond the limits of the screen.

In order to use a ScrollView in the code these steps are needed.

``` swift
    \\1
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
https://useyourloaf.com/blog/changing-root-view-layout-margins/003.png

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
### Autolayout Errors






 



