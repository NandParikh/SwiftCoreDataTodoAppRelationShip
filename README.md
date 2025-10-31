

https://github.com/user-attachments/assets/0c67e283-2680-4540-8779-fd4d8a12536c


<img width="1632" height="998" alt="Screenshot 2025-10-31 at 4 31 22 PM" src="https://github.com/user-attachments/assets/b4769ac2-be98-42e3-b556-ea21fad786a6" />


<img width="1647" height="996" alt="Screenshot 2025-10-31 at 4 31 34 PM" src="https://github.com/user-attachments/assets/ea30723e-b106-493a-bd11-c7659ec4668f" />


<img width="1646" height="1001" alt="Screenshot 2025-10-31 at 4 32 10 PM" src="https://github.com/user-attachments/assets/cd19b63f-b479-4146-9413-4a02b0e137ff" />



# Core Data in iOS (Complete Guide)

## 📘 What is Core Data?

**Core Data** is Apple's **object graph management and persistence
framework**.\
It lets you **store, fetch, update, and delete** objects in a local
persistent store (usually an SQLite database).\
Core Data manages data using objects --- so you work with Swift classes
instead of SQL queries.

------------------------------------------------------------------------

## 🧩 Core Data Architecture Components

### 1. **NSManagedObjectModel**

-   Represents your **data model (entities, attributes, and
    relationships)**.
-   Each entity is a **table**, and each object is a **row** in that
    table.

### 2. **NSManagedObjectContext**

-   Acts as an **in-memory workspace** where you interact with your
    data.
-   You can **create, fetch, update, and delete** objects here.
-   Changes are saved to the persistent store via the **Persistent Store
    Coordinator**.

### 3. **NSPersistentStoreCoordinator**

-   Bridges the **Managed Object Context** and the **Persistent Store
    (like SQLite)**.
-   Manages the actual storage --- SQLite, XML, or binary files.
-   Controls saving, fetching, and synchronization between the context
    and database.

------------------------------------------------------------------------

## ⚙️ Core Data Setup Process

1.  **Create a new project** and check **"Use Core Data"**.
2.  Add an **Entity** (like `Employee`, `Passport`, etc.).
3.  Add **Attributes** (like `id`, `name`, `email`).
4.  Set up **Relationships** between entities.

------------------------------------------------------------------------

## 🧠 Codegen Types

  -----------------------------------------------------------------------
  Type                  Description
  --------------------- -------------------------------------------------
  **Automatic**         Xcode auto-generates Core Data classes.

  **Manual/None**       You manually create subclass files using:
                        `<br>`{=html}
                        `Editor → Create NSManagedObject Subclass`.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 🖼️ Storing Images in Core Data

To store images: - Set the attribute type to **Binary Data**. - Enable
**"Allows External Storage"** (stores image in a derived folder).

``` swift
employee.picture = image.pngData()
```

------------------------------------------------------------------------

## 🔗 Core Data Relationships

### Common Relationship Types

  ------------------------------------------------------------------------
  Relationship Type                Example          Description
  -------------------------------- ---------------- ----------------------
  **One to One**                   Employee ↔       One employee has one
                                   Passport         passport

  **One to Many**                  Person ↔         One person has
                                   Vehicles         multiple vehicles

  **Many to Many**                 Student ↔ Course Many students can
                                                    enroll in many courses
  ------------------------------------------------------------------------

### Example:

**Employee → Passport (One to One)**

``` text
Employee
  - id
  - name
  - email
  - picture (Binary Data)
  Relationship: toPassport → Passport (Inverse: toEmployee)

Passport
  - id
  - passportId
  Relationship: toEmployee → Employee (Inverse: toPassport)
```

### Delete Rules

  Rule            Description
  --------------- ----------------------------------------------
  **No Action**   Does nothing on deletion
  **Nullify**     Clears the relationship (keeps child record)
  **Cascade**     Deletes associated child records
  **Deny**        Prevents deletion if relationships exist

------------------------------------------------------------------------

## 💾 Basic Core Data Code Examples

### Get Context

``` swift
let context = (UIApplication.shared.delegate as! AppDelegate).persistentContainer.viewContext
```

### Save Data

``` swift
let newCategory = ProductCategory(context: context)
newCategory.name = "Shopping"
try? context.save()
```

### Fetch Data

``` swift
let request: NSFetchRequest<ProductCategory> = ProductCategory.fetchRequest()
let results = try? context.fetch(request)
```

### Delete Data

``` swift
context.delete(categoryToDelete)
try? context.save()
```

------------------------------------------------------------------------

## 🧮 Example: Category and Item Relationship

**CategoryVC.swift**

``` swift
func saveCategories() {
    do {
        try context.save()
    } catch {
        print("Error saving category: \(error)")
    }
}
func loadCategories() {
    let request: NSFetchRequest<ProductCategory> = ProductCategory.fetchRequest()
    categories = try! context.fetch(request)
}
```

**TodoListVC.swift**

``` swift
let newItem = Item(context: context)
newItem.title = "Buy Milk"
newItem.done = false
newItem.productCategory = selectedCategory
try? context.save()
```

------------------------------------------------------------------------

## 🧱 Core Data Stack (AppDelegate)

``` swift
lazy var persistentContainer: NSPersistentContainer = {
    let container = NSPersistentContainer(name: "DataModel")
    container.loadPersistentStores { _, error in
        if let error = error as NSError? {
            fatalError("Unresolved error \(error)")
        }
    }
    return container
}()
```

------------------------------------------------------------------------

## ⚖️ Core Data vs Realm vs SQLite

  -------------------------------------------------------------------------------
  Feature             **Core Data**         **Realm**          **SQLite**
  ------------------- --------------------- ------------------ ------------------
  **Type**            Framework             Database           Database

  **Language**        Object‑based          Object‑based       SQL Queries

  **Performance**     Moderate              Very fast          Fast (manual
                                                               management)

  **Relationships**   Native support        Native support     Manual joins
                                                               required

  **Data Model**      xcdatamodeld          Classes/Subclass   Tables

  **Ease of Use**     Medium                Easy               Harder (requires
                                                               SQL)

  **Persistence**     Built‑in              Built‑in           Manual
  -------------------------------------------------------------------------------

------------------------------------------------------------------------

## 🧠 Key Notes

  ---------------------------------------------------------------------------
  Concept                            Explanation
  ---------------------------------- ----------------------------------------
  **Core Data**                      Apple's framework for data persistence
                                     using objects

  **NSManagedObjectModel**           Defines structure (entities,
                                     relationships)

  **NSManagedObjectContext**         Interface to access or modify data

  **NSPersistentStoreCoordinator**   Connects data with SQLite or XML files

  **Binary Data Storage**            Use for storing images as blobs

  **Delete Rules**                   Control cascading deletes between
                                     related entities

  **FetchRequest**                   Used to retrieve records from Core Data

  **Predicate**                      Used for filtering results during fetch
  ---------------------------------------------------------------------------

------------------------------------------------------------------------

## 🧩 Quick Demo Code

``` swift
let request: NSFetchRequest<Item> = Item.fetchRequest()
let predicate = NSPredicate(format: "title CONTAINS[cd] %@", "Milk")
request.sortDescriptors = [NSSortDescriptor(key: "title", ascending: true)]
loadItems(with: request, predicate: predicate)
```

------------------------------------------------------------------------

## ✅ Summary

Core Data simplifies data persistence by allowing developers to manage
structured data using Swift objects --- without manually writing SQL
queries. It supports relationships, cascading deletes, predicates, and
integration with UIKit.

------------------------------------------------------------------------
