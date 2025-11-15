# <img src="readme_files/pathfinder_logo.svg" style="width: 1em; height: auto;" alt="Pathfinder" /> Pathfinder

An application designed to **visualize and analyze relationships** between Workday **business objects** through an **interactive graph**. It enables users to **discover possible links** between business objects and supports the creation of **ARI** and **LRV + ESI** calculated fields.

## 🎯 Objective

Workday stores data in business objects. Business objects have fields and instances, which are similar to columns and rows in a spreadsheet. Each row is an instance, and each column represents a field. The Workday object model can be conceptualized as a [graph](https://en.wikipedia.org/wiki/Graph_database), with business objects as nodes and fields as edges that connect them.

In the example below, the **Supplier Created By** field available on the **Supplier** business object is an instance field that links to a specific Worker in the **Worker** business object. This field identifies the Worker who created the Supplier and allows retrieving all other information related to that Worker from the Worker object onto the Supplier object. In other words, this field — available on the Supplier spreadsheet — acts as a bridge to bring in Worker data from Worker spreadsheet onto the Supplier spreadsheet.
 
```json
{
  "Business Object": "Supplier",
  "Related Business Object": "Worker",
  "Field": "Supplier Created By"
}
```

In a report based on Supplier, the **Employee ID** of the Worker who created that Supplier may need to be retrieved. Since the Employee ID is not available directly on the Supplier object, accessing it requires moving from the Supplier object to the Worker object and retrieving the Employee ID field. The process of moving from one object to another within a complex network of interconnected objects is known as **Traversal**.

But what if the required data is not readily available on the primary or the immediate related business objects? What if the path to get to the required data is unknown? This is where **Pathfinder** (inspired from [Pathfinding](https://en.wikipedia.org/wiki/Pathfinding)) comes into play. Given two business objects — a source and a target — along with a depth level (representing distance in a traditional sense), it identifies and displays possible paths to navigate objects and retrieve the required data within that distance. It also features a canvas that enables users to add and remove business objects while dynamically updating the relationships between them in real time. 

## ⚙️ Setup

To use the application, an end-user only needs to download the latest object relationships data from the tenant and attach the resulting files during runtime. To extract most relationships from the tenant, three reports must be created. The instructions for creating these reports are provided below. Sample JSON files are available on [Community](https://collaborate.workday.com/t5/General/Visualize-and-Analyze-Relationships-Between-Business-Objects/ta-p/1960736) for quick testing but are outdated. 

### Download Object Relationships from Tenant

#### Report #1: All Fields Report and JSON
* Create an **Advanced** custom report using **All Fields** data source. Enable the report as a web service.
* Create a **Concatenate Text** calculated field with the name **CF TC Field Name** on **Field** business object as shown below:
  | Field to Concatenate         | Field Type                  |
  |------------------------------|-----------------------------|
  | Field                        | Self-referencing instance   |
  | Single Space                 | Text                        |
  | (                            | Text                        |
  | Report Field Type            | Text                        |
  | )                            | Text                        |
* Add the following fields/Columns to the report:
  | Business Object | Field                      | Column Heading Override XML Alias |
  |-----------------|----------------------------|-----------------------------------|
  | Field           | Business Object            | B                                 |
  | Field           | CF TC Field Name           | F                                 |
  | Field           | Related Business Object    | R                                 |
* Add the following Filter conditions:
  * **Business Object** `is not empty`
  * **Related Business Object** `is not empty`
  * **Authorized Usage** `any in the selection list` `Value specified in this filter` ->select all except those marked internal 
* Save the report.
* Click on related actions of the report and navigate to **Web Service** > **View URLs**. Click on the JSON link and enter credentials to download the JSON.

#### Report #2: All Data Sources Report and JSON
* Create an **Advanced** custom report using **All Data Sources** data source. Enable the report as a web service.
* Create a **Concatenate Text** calculated field with the name **CF TC Field Name** on **Field** business object as shown below:
  | Field to Concatenate         | Field Type                  |
  |------------------------------|-----------------------------|
  | Field                        | Self-referencing instance   |
  | Single Space                 | Text                        |
  | (                            | Text                        |
  | Report Field Type            | Text                        |
  | )                            | Text                        |
* Add the following fields/Columns to the report:
  | Business Object            | Field                      | Column Heading Override XML Alias |
  |----------------------------|----------------------------|-----------------------------------|
  | Data Source                | Primary Business Object    | B                                 |
  | All Report Fields          | CF TC Field Name           | F                                 |
  | All Report Fields          | Related Business Object    | R                                 |
* Add the following Group Column Headings:
  | Business Object   | XML Alias |
  |-------------------|-----------|
  | All Report Fields | R_G       |
* Add the following Filter conditions:
  * **All Report Fields** `is not empty`
  * **Primary Business Object** `is not blank`
* Add the following Subfilter conditions on the **All Report Fields** Business Object:
  * **Related Business Object** `is not empty`
  * **Authorized Usage** `any in the selection list` `Value specified in this filter` ->select all except those marked internal 
* Save the report.
* Click on related actions of the report and navigate to **Web Service** > **View URLs**. Click on the JSON link and enter credentials to download the JSON.

#### Report #3: Classes Report and JSON
* Create an **Advanced** custom report using **Classes** data source. Enable the report as a web service.
* Create a **Concatenate Text** calculated field with the name **CF TC Field Name** on **Field** business object as shown below:
  | Field to Concatenate         | Field Type                  |
  |------------------------------|-----------------------------|
  | Field                        | Self-referencing instance   |
  | Single Space                 | Text                        |
  | (                            | Text                        |
  | Report Field Type            | Text                        |
  | )                            | Text                        |
* Add the following fields/Columns to the report:
  | Business Object                                 | Field                      | Column Heading Override XML Alias |
  |-------------------------------------------------|----------------------------|-----------------------------------|
  | Class                                           | Business Object Name       | B                                 |
  | Class Report Fields for Class and Super Classes | CF TC Field Name           | F                                 |
  | Class Report Fields for Class and Super Classes | Related Business Object    | R                                 |
* Add the following Group Column Headings:
  | Business Object                                 | XML Alias |
  |-------------------------------------------------|-----------|
  | Class Report Fields for Class and Super Classes | R_G       |
* Add the following Filter conditions:
  * **Class Report Fields for Class and Super Classes** `is not empty`
  * _[Optional]_ **Class Name** `equal to` `Value from another field` **Business Object Name** 
* Add the following Subfilter conditions on the **Class Report Fields for Class and Super Classes** Business Object:
  * **Related Business Object** `is not empty`
  * **Authorized Usage** `any in the selection list` `Value specified in this filter` ->select all except those marked internal 
* Save the report.
* Click on related actions of the report and navigate to **Web Service** > **View URLs**. Click on the JSON link and enter credentials to download the JSON.

#### Additional Considerations
* There are some business objects that [share the same name](https://collaborate.workday.com/t5/General/Visualize-and-Analyze-Relationships-Between-Business-Objects/tac-p/1976592/highlight/true#M12744) despite being completely different. Even though the number of such cases is small, for increased accuracy, append the last six characters of the WID to the business object names so that each one remains truly unique in the context of this application.

### Run the application 
Navigate to [https://sy276.github.io/pathfinder/pathfinder.html](https://sy276.github.io/pathfinder/pathfinder.html) to access the application. Alternatively, the latest version can be downloaded [here](https://github.com/sy276/pathfinder/blob/main/pathfinder.html). The [hosted version](https://sy276.github.io/pathfinder/pathfinder.html) on github.io **is recommended**, as it stays up to date with the latest changes. Regardless of which version is used, all data is processed on the client side (i.e., on the local computer). 

## 📚 User Guide

* The first step is attaching the JSON files on page load. When the **⚡Upload files to get started.** prompt appears, the JSON files downloaded from the tenant should be attached.
  * **For optimal performance and accuracy**, attach the **All Fields** and **All Data Sources** JSON files **first**. If the desired traversal paths are not returned, attach the **Classes** JSON file in addition to the other two files. The **Classes** file should be used as a **last resort**, as it often contains relationships that may not always work as expected. 
* On the next page
  * The most commonly occurring business object is displayed on the graph. Clicking on this business object reveals connected business objects with links to and from it. From that menu, immediate neighbouring business objects can be added to the graph to establish a path. This process can be repeated to continue establishing paths. Clicking on these paths displays the list of fields connecting the business objects.
  * The interface also features two buttons - **Fileloader** and **Pathfinder** - along with a search bar.
  * The search bar enables searching for business objects and allows for adding, removing, and locating these objects on the graph.
  * **Fileloader** allows for the upload of additional files that may not have been added initially.
  * **Pathfinder** enables the discovery of potential links between two business objects.
    * The **Pathfinder** screen features three input fields - **Source Object**, **Target Object**, **Depth Level** - along with a **Find Paths** button.
    * **Source Object** represents the starting point of your search. When creating calculated fields, the Source Object is the business object onto which you want to bring a specific field. For example, if you're building a report based on the **Worker** business object and need to include the **Additional Nationalities** field from the **Dependent** business object, the Source Object will be Worker. This field is case-sensitive.
    * **Target Object** is the destination of your search. Using the previous example, the **Dependent** business object would be the Target Object. This field is case-sensitive.
    * **Depth Level** defines how many levels deep the search should traverse to locate the desired business object. In the above example, reaching the **Dependent** business object requires traversing one level. If the available paths at a given depth level do not meet your needs, you can specify a higher number. The minimum Depth Level is 1, and while there is no maximum limit, increasing the depth significantly extends processing time due to exponential complexity.
    * Clicking **Find Paths** shows possible connections from Source Object to Target Object within the specified Depth Level.
      * Every path features two buttons - **Plot Path** and **Field Paths**.
      * **Plot Path** adds the business objects from the selected path to the graph and updates connections between all business objects present on the graph.
      * **Field Paths** displays possible field combinations that connect business objects from the selected path. Since multiple fields can link the same business objects, this screen presents every potential path, showing how fields establish those connections.
        * Each path includes a **Calculated Fields** button that, when clicked, displays instructions for creating calculated fields for the selected path. Users can choose between **LRV + ESI** or **ARI** calculated fields.

## ⚠️ Limitations

Extracting object relationships is challenging because object-to-object and object-to-field relationships are not fully exposed through any report data sources. While the three reports above allow us to extract most of these relationships, there may still be more in the tenant that are not captured. There may also be broken relationships that, despite appearing in the application, do not always function in the tenant. Most of these issues arise from how the relationships are sourced. Unfortunately, no better alternative has been identified at this time, and as efforts to explore this area continue, the three reports should be considered a good starting point rather than a complete solution. 