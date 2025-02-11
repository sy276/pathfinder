* **Classes JSON** 
  * Create a custom report using **Classes** RDS, download the JSON file, and attach it here.
* **All Fields JSON** 
  * Create a custom report using **All Fields** RDS, download the JSON file, and attach it here.
* **All Data Sources JSON** 
  * Create a custom report using **All Data Sources** RDS, download the JSON file, and attach it here.
* **Target BO**
  * This will be the business object you will pull your desired field onto. For example, if you are building a report on **Worker** business object and want to pull a field **Additional Nationalities** from the **Dependent** business object onto your report, the Target Business Object will be **Worker**. This field is case sensitive.
* **Source BO**
  * In case of the above example, your Source Business Object will be **Dependent**. This field is case sensitive.
* **Depth Level**
  * How many levels of business objects do you want to traverse through in order to get to your desired business object? In the above example, you will be able to get to the **Dependent** business object by traversing through two levels, but you may choose to enter any higher number if you are not satisfied with the traversal paths at a certain depth level. The minimum Depth Level is 2. There is no limitation on maximum Depth Level, but processing takes exponentially longer as you keep going higher and higher.
* **Get Business Object Relationships**
  * Click this button to get relationships between business objects. This will give you all the traversal paths between your Target BO and Source BO up to a certain Depth Level. 
* **BO Traversal Path**
  * Usually, there’s more than one traversal path between two business objects. Select a business object traversal path that works for your requirement/use case. In this dropdown, you’ll see only business object level data. BO traversal paths look like this – **B1 --> B2 --> B3 --> B4**. Here B1, B2, B3, B4 are business objects.
* **BO-Field Traversal Path**
  * Fields connect business objects. There can be more than one field that connects two business objects. Like the BO traversal path, you will have to select a traversal path that works for your requirement/use case. In this dropdown, you’ll see business object and field level data. BO-Field traversal paths look like this – **B1 --> F1 --> B2 --> F2 --> B3 --> F3 --> B4**. Here B1, B2, B3, B4 are business objects and F1, F2, F3 are fields.
* **Calculated Fields**
  * Here you will see the steps to create the calculated fields.