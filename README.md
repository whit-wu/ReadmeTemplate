# [App Name]

 

## Description

[Place a description of your app here]

 

## Link to Environments

[App Name]'s Dev, Test, and Production environments are located at the following locations:

- Dev: [Dev URL]

- Test: [Test URL]

- Production: [Prod URL]

 

## Dependencies

To run [App Name], the following frameworks must be installed on your system:

- .NET Core v[version number here] 

- .NET Framework v[version number here]

- Node.js v[version number here]

- Typewriter (if developing with Visual Studio)

- Any other dependency not listed

 

## Getting Started

 

### Angular Setup

Open a Powershell/Command Prompt window and navigate to the ClientApp directory.  From there, run `npm install` to install all the dependencies required by Angular.  

 

### Creating/Connecting to a Local Database

The dev/test/prod databases for [App Name] have been named [database name here], and stored in Azure under the following server addresses:

 

- Dev: [database server address here]

- Test: [database server address here]

- Prod: [database server address here]

 

Since these databases are associated with their own corresponding app services, it is best to make a clone of at least one of them for local development use.  This helps ensure that users on each server are not impacted by app changes and allows you to continue development in the event Azure cannot be reached.  To do this, please adhere to the following steps:

 

1. Connect to the "BDO USA - VPN Full" VPN with Cisco AnyConnect.

1. Open SSMS.  In the Registered Servers menu, expand Database Engine and right-click on Local Server Groups, then select New Server Registration.

1. In the General tab, place the address for your database in the Server name box, then select "Azure Active Directory - Universal with MFA" in the Authentication drop-down.  

1. In the Connection Properties tab, select/type the name of your database in the Connect to database box, then click on the Test button at the bottom.  If you test completed successfully, click Save.  Otherwise, ensure you are on the correct VPN connection and are using the correct server address/database.

1. Expand the Local Server Groups to reveal your database server connections.  Double-click on the server your database is stored on to open it in the Object Explorer.  Sign in with Azure MFA, if prompted.

1. In the Object Explorer, expand Databases.  Right-click on your database, navigate to Tasks, and select Export Data-tier application.  The Export Data for Application wizard should appear.

1. In the wizard, you will notice there are four categories you will be walked through: Introduction, Export Settings, Summary, and Results.  Perform the following operations on each category:

    - Introduction: Click Next

    - Export Settings: In the settings tab, ensure the "Save to local disk" radio button is selected, and select where you want to save your database clone and what you want to call the .bacpac file it will be stored in.  Please note that the .bacpac file name and the database name are treated differently, meaning you will not be using the name you give this file in your connection string.

    - Summary: Verify your settings are correct, then click Finish.

    - Results: Your database will be exported.  When the operation is complete, click close.

1. Once your database has been exported to a BACPAC file, expand localhost in the Object Explorer menu.  Right-click on the Databases folder and select "Import Data-tier application".  This will open the Import Data-tier Application Wizard.

1. In the Import Data-tier Application Wizard, you will notice there are four categories you will be walked through: Introduction, Export Settings, Summary, and Results.  Perform the following operations on each category:

    - _Introduction_: Click Next

    - _Import Settings_: Select the "Import from local disk" radio button, then click browse to locate the BACPAC file you exported.  Once you have specified the location of your BACPAC file, click Next.

    - _Database Settings_: In the "New database name" box, type "name of database".  Then click Next.

    - _Summary_: Verify your settings are correct, then click Finish.

    - _Results_: Your database will be imported.  When the operation is complete, click close.

1.  [For .NET Core app] Connection strings are stored in the appsettings.json file for web projects (if your app makes use of an Azure Function, you will need to add the connection string to the project's local.setting.json file as well). These will be treated as key/value pairs with the name of the connection string as the key, and the connection string itself as the value.  In [App name], the key for the connection string is called [name of key].  To route your app to your local database, add the following code to the connection strings category of your json file:

    

    `"[name of key]" : "Server=.;Database=[name of database];Trusted_Connection=True;MultipleActiveResultSets=true"`  

 

    [For .NET Framework app] Connection strings are stored in the app.config file for web projects and the local.settings.json for Azure Function projects.  For app.config files, the name property will be used as the key to the connection string, and the server property will be used as the connection string.  To add a new connection string to a web project, open your app.config file in your web project, search for the connection strings category, and place the following XML inside:

    

    `<add name="[name of key]" providerName="System.Data.SqlClient" connectionString="Server=localhost;Initial Catalog=ExpatTaxCalculator;Integrated Security=SSPI;" />`  

   

    If you are making use of an Azure function, your connection string will need to be added to the project's local.settings.json file.  The JSON will be treated as a key/value pair with the name of the connection string as the key, and the connection string itself as the value.  In [App name], the key for the connection string is called [name of key].  To route your Azure function to your local database, add the following code to the connection strings category of your local.settings.json file:

    

    `"[name of key]" : "Server=.;Database=[name of database];Trusted_Connection=True;MultipleActiveResultSets=true"`  

 

## Editing the Database with Entity Framework

### How to Change the Database Schema (Tables and Fields):

1. Change your model as necessary.

1. In the Package Manager Console, switch the Default Project dropdown to Bdo.[App Name].Data, then type in this command:

**add-migration ADescriptiveName**

1. Inspect the generated C# migration file to confirm it's going to do what you expect.

1. Run your project or use the command **update-database** to apply changes.

 

1. [if using .scmp file]Open the [FileName].scmp file (if you have one) in the Bdo.[App Name].Database project, and click Compare.  Then click Update when it's done. _This saves your migration to the database schema that is deployed on Azure._

1. Using SQL Management Studio, edit the __EFMigrationsHistory table and copy the last row (it should contain the name you used for your migration in the first column) to the dev database's table.  _This lets us clone the dev database onto our local machines without any build errors._ 

 

### [if using .scmp file] How to Change Stored Procedures, Functions, etc.:

1. Create/edit the item on your local database

1. Open the [FileName].scmp file in the Bdo.[App Name].Database project (ensure it's still set to source of localhost, target of Bdo.[App Name].Database), and click Compare.

1. Click Update when it's done. This saves your migration to the database schema that is deployed on Azure.

 

### [if using .scmp file] How to Update Your Local Database with Other Developers' Changes:

1. Open the [FileName].scmp file in the Bdo.[App Name].Database project, click the <--> arrows between source and target so that you're targeting your local database, and click Compare.

1. Click Update when it's done.

 

## Using Typewriter to Communicate Between Front/Back Ends

[App Name] makes use of Typewriter, a Visual Studio extension that will generate Typescript for your front-end to call when you create new methods in your C# controller(s).  The generated code is saved in a file, called `bridge.service.ts`.  Any code generated there should be called from your `unit-of-work.service.ts` file, and not by components directly.  To make use of Typewriter, perform the following operations:

1. Make the necessary changes to your controller.

1. Build the application by pressing `Ctrl + Shift + B` in Visual Studio.  This will generate the call to your controller in `bridge.service.ts`

1. Inspect the generated call in `bridge.service.ts`.  If it looks correct, call it from your `unit-of-work.service.ts` file so that it can be accessible to your components.  

 

NOTE: If you are developing with Visual Studio Code, you will need to edit the `bridge.service.ts` manually, as Typewriter is not supported.   

 

## Known Issues

[Share known problems a dev could encounter with this application along with ways to resolve them]
