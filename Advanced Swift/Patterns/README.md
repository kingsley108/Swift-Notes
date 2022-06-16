# Patterns

## Inheritance

When creating a subclass you can't do anything until you initialize all stored varaibles and also call & initialsie the parents class initilizers, otherwise you will get an error from the compiler.

```swift
init(name: String, title: String, currentPatient: String) {
       self.title = title //Initialise stored variables
       super.init(name: name, currentPatient: currentPatient) //Initialise and call the parent class.
}
```

## Protocol Oriented Programming

In OOP, the contrast between inheritance and composition is often called "is-a vs has-a". A surgeon is a doctor, so it makes sense to inherit Surgeon from Doctor. A surgeon has a scalpel, which means the Surgeon class could have a property called scalpel, but you wouldnt want a surgeon to inerit from a knife

A protocol is a promise of functionality, the potocol does not contain the actual implementation but the comiler will ensure that anyone who conforms to the protocol must implement all the methods you specify.

```swift
protocol Salary {
    func calculateTax(annualSalary: Double) -> Double
}
```

A protocol can also have the default implementation, the default implementation of the protocol **_Salary_** can be found below.

```swift
extension Salary {

    //This is the default implementation
    func calculateTax(annualSalary: Double) -> Double {
        annualSalary * 0.5
    }
}
```

## POP in Practice

**POP (Protocol Oriented Programming)** also lets you add only methods and coputed properties, not stored properties.This means state is not in your code meaning complexity is low.

A protocol can adopt other protocol, an example is modelling a university architechture any member of qmmplus whether student or staff will need to have the same acess such as to the intranet, a library card and also ability to be paid for doing some sort of work.

```swift
protocol NeedsEAcess {}
protocol HasLibraryCard {}
protocol HasRoomAcess {}
protocol CanGraduate {}
protocol Payable {}
protocol Rentable {}
protocol QMMember: NeedsEAcess, HasLibraryCard, Payable {}
```

POP also allows us to use structs instead of classes as structs can not inherit. Using our example below we can adopt the protocols and behaviour above.

```swift
struct Student: QMMember,Rentable, CanGraduate  {}
struct Staff: QMMember, HasRoomAcess {}
```

## Constrained Extensions

To specify who can get a certain piece of functionality we can extend that protocol meaning the extension only applies to a certain type of protocols, in this case we want a University Member that also can rent (conforms to rentable).

```swift
extension QMMember where Self: Rentable {
    //Functionaliy here
}
```

## MVC

MVC or Model View Controller is the standardized approach for building application.

- Models are where the business logic lies and is responsible for saving the data.
- Views is resposible for the rendering of objects to the UI Interface such as `UIButton` and `UITableView`
- Controllers are resposnible for pulling the data from the model and rendering it in the UI Interface and additionally retrieve user input and save this to the model objects.

The beauty of the String, is you no longer need to have hard coded Strings spread throughout your library. You can now capture those as enum types and switch on them very conveniently.

## MVVM

The ViewModel hides all the asynchronous networking code, data preparation for visual presentation and code listening from the Model, and also handles updating the UI or updating the model.

In the code below the ViewModel gets the employeeData from the APIService and then sets it to a variable which can be acessed by the viewcontroller, when the variable is set it calls the `bindEmployeeViewModelToController` closure which acts as a notification system to the viewController to call the updateDataSourceMethod() 

```swift
class EmployeesViewModel : NSObject {
    
    private var apiService : APIService!
    private(set) var empData : Employees! {
        didSet {
            self.bindEmployeeViewModelToController()
        }
    }
    
    var bindEmployeeViewModelToController : (() -> ()) = {}
    
    override init() {
        super.init()
        self.apiService =  APIService()
        callFuncToGetEmpData()
    }
    
    func callFuncToGetEmpData() {
        self.apiService.apiToGetEmployeeData { (empData) in
            self.empData = empData
        }
    }
}
```

ViewController Code

```swift
override func viewDidLoad() {
        super.viewDidLoad()
        callToViewModelForUIUpdate()
    }
    
    func callToViewModelForUIUpdate(){
        
        self.employeeViewModel =  EmployeesViewModel()
        self.employeeViewModel.bindEmployeeViewModelToController = {
            self.updateDataSource()
        }
    }
    
    func updateDataSource() {
        //Update View
    }
```
