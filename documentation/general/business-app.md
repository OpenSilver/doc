## Creating a Business Application with RIA Services

RIA Services (Rich Internet Application Services) is a powerful framework that simplifies the development of data-driven applications for the web. With RIA Services, you can easily create business applications that offer rich user experiences and efficient data access. This documentation will guide you through the process of creating a business application using OpenRIA Services (the evolved Open Source version of WCF RIA Services) using OpenSilver.

OpenSilver includes a project template that lets you leverage Open RIA Services to easily create complex client/server business applications.

### Getting Started
You will need Visual Studio 2022 (or newer). Install the OpenSilver VSIX Templates from [here](https://opensilver.net/download.aspx). These templates include the OpenSilver Business Application Template. 

### Tutorial

You can see a quick video demo from this tutorial here:
> [!Video https://www.youtube.com/embed/ZyctFzWKda8?start=345&end=462]

#### Detailed Steps

1. Configure the Database  
For this tutorial, we will use the AdventureWorks Lightweight database (AdventureWorksLT2022.bak). You can see instructions on how to download, restore, and setup here:
[https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

2. Create a new OpenSilver Business Application Project
![OpenSilver Ria Services Business Application](/images/ria-business01.png)

3. In Visual Studios's Solution Explorer, right-click the .Web project, click Add, and then click New Item.

4. Select the "ADO.NET Entity Data Model" template. Change the name to "AdventureWorks", and then click Add.
![ADO.NET Entity Data Model](/images/ria-business02.png)

5. On the Choose Model Contents page, click "EF Designer from database", and then click Next.
![EF Designer from database](/images/ria-business03.png)

6. <a name="database-connection"></a>On the Choose Your Data Connection page, create a connection to the AdventureWorks database.
![Connection Properties](/images/ria-business04.png)

    * Copy the ConnectionString, and edit it like this:
    ```
    - Replace any double quotes with &quot;
    - Replace 'data source' with 'Server'
    ```    
    ![Choose Your Data Connection](/images/ria-business05.png)  

    * Go to Web.config file, and add this new entry into the <connectionStrings>> section, using the updated connection string from the previous step:
    ```xml
    <add name="AdventureWorksLT2022Entities" 
         connectionString="<paste the edited connectionString here>" 
         providerName="System.Data.EntityClient" />
    ```

7. On the Choose Your Database Objects page, expand the Tables node, select everything from the SalesLT schema, and click Finish

![Choose Your Database Objects](/images/ria-business06.png)

8. The database is configured in the .Web project and the entity data model appears in the designer (Build the solution).
![Data Model](/images/ria-business07.png)

9. Now that we configured the database, let's proceed to the Open RIA configuration. In Solution Explorer, right-click the .Web project, click Add, and then click New Item, select the "Domain Service" template. Name the new item "OrganizationService".
![Add New Domain Item](/images/ria-business08.png)

10. In the Add New Domain Service Class dialog box, select Customers from the Entities list.

Note: if the list is empty, please rebuild the solution, and open the dialog again.

Ensure that the "Enable client access" and "Generate associated classes for metadata" check boxes are selected. Click Ok.  
![Add New Domain Service](/images/ria-business09.png)

11. Build the solution.  
Building the solution generates the Domain Context and entities in the client project.  
On the client side, the Domain `Service` class has the suffix `Context`. So the `OrganizationService` becomes `OrganizationContext`.  
![Services and Contexts](/images/ria-business10.png)

12. Open file `Views\Home.xaml`, and include this code inside of the StackPanel:
```xml
<DataGrid x:Name="dataGridCustomers"
          Height="200"/>

<DataForm CurrentItem="{Binding ElementName=dataGridCustomers, Path=SelectedItem}" 
          Height="200"/>
``` 
The result will be like this:
![Xaml Code](/images/ria-business11.png)

13. And in `Views\Home.xaml.cs`, include this private field:
```c#
private readonly OrganizationContext _context = new OrganizationContext();
```        
and after `InitializeComponent()` call, add this:
```c#
dataGridCustomers.ItemsSource = _context.Customers;
_context.Load(_context.GetCustomersQuery());
```
The result will be like this:
![C# Code](/images/ria-business12.png)

14. Execute the ".Web" project, then execute the ".Browser" project and check the results:
![Final](/images/ria-business13.png)

### Troubleshooting and Known Issues

#### Running the application:
* Make sure to run both the .Web project (for the server side) and .Browser project (for the client side)
* If you have an error that says "**No connection string named 'cshtml5dbEntities' could be found in the application config file.**", make sure that you have added an entry to the Web.Config file as explained at [this step](#database-connection).
* If your application doesn't run or has some runtime errors, open the browser console (F12) and look at the errors listed there
* If you see an error related to CORS (such as "**Access to fetch at 'http://localhost:54837/...' from origin 'http://localhost:55591' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**"), then [configure CORS on the server-side](https://doc.opensilver.net/documentation/in-depth-topics/wcf-and-webclient.html#adding-support-for-cross-domain-calls-cors), or work around CORS entirely by [running your browser with disabled security](https://simplelocalize.io/blog/posts/what-is-cors/#3-disable-browser-cors-checks)
* If you receive `Unable to load DLL 'SQLite.Interop.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` error, this is a known issue where SQLite sometimes does not generate x86/x64 folders inside of the bin folder of the .Web project. Most of the times you just need to Clean and then Build the project until these folders are generated.
![SQLite Interop Error](/images/ria-business14.png)

#### Creating the application:
* If you don't see the "Add New Domain Service Class" dialog, make sure to install the latest VSIX of OpenSilver.
* If the "Add New Domain Service Class" dialog is empty, make sure to rebuild the solution, and try again.

### See Also
* [OpenSilver Information about Open RIA Services](https://doc.opensilver.net/documentation/3rd-party-libraries/ria-services.html)  
* [OpenSilver SampleCRM Business Demo Application](https://github.com/OpenSilver/SampleCRM)
* [Microsoft original documentation for WCF RIA Services](https://learn.microsoft.com/en-us/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91))
* [Open RIA Services Wiki](https://github.com/OpenRIAServices/OpenRiaServices/wiki)
* [Open RIA Services Documentation](https://openriaservices.gitbook.io/openriaservices/)