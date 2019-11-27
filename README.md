# BlazorGeo
# BlazorGeo using AspNetMonsters.Blazor.Geolocation
This package uses AspNetMonsters.Blazor.Geolocation to get get geolocation data.  Is uses Version 0.5.0-preview1.

When attempting to use AspNetMonsters.Blazor.Geolocation, a number of problems were encountered.  This application
overcame those obstacles, and is presented as a guide others.

## Requirements
This application was built with the following:
1) Visual Studio Version 16.3.9
2) .NET Core 3.0
3)  AspNetMonsters.Blazor.Geolocation Version 0.5.0-preview1

## Build From Scratch
1) Build a default Blazor App
   1) Launch Visual Studio 2019, V16.3.9 or higher.
   1) In the `What would you like to do?` dialog, select `Create a new Project`
   1) In the `Create a new project` dialog, select 'Blazor App'  Then click `Next`.
   1) On the `Configure your new project` dialog, choose a Project Name.  Then click on `Create.`
   1) On the `Create a new Blazop App` dialog, make sure `Blazor Server App` is highlighted.  Then click on `Create.
1) Install AspNetMonsters.Blazor.Geolocation
   1) Once the application is built, go to Package Manager Console and enter
   
    ```
    Install-Package AspNetMonsters.Blazor.Geolocation -IncludePrerelease
    ```
    2) -IncludePrerelease is required.
   
1) Edit Program.cs.  Replace `... CreateHostBuilder...` with the following:
    
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>()
                        .UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
                });
    2) DetailedErrorsKey will provide more descriptive error messages in 
    the browser Console to aid debugging when using SignalR and Blazor.   Be sure to turn it off for anything going into production.

1) Edit `Startup.cs`
   1) Register AspMonsters.Blazor.Geolocation at the top of the file.
    ```
    using AspNetMonsters.Blazor.Geolocation;
    ```

   2)  register the 'LocationService'.

    ```
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddScoped<LocationService>();
        ...
    }
    ```
    3)   Using `AddSingleton` causes a `System.AggregateException` in `Program.cs:Main:CreateHostBuild` because `iJSRuntime` is already registered as a scoped service.  
    4)   Because `WebHostDefaults.DetailedErrorsKey` is true, the detailed error can be seen by changing `AddScoped` to `AddSingleton` and running the application.

1) Edit `Index.razor`
   1) Use Dependency Injection to register the location service.
        ```
        @using AspNetMonsters.Blazor.Geolocation
        @inject LocationService  locationService
        ```
        2) Take note that the local variable name is locationService with a lowercase l.  
        3) This insures there is no confusion between the Class and the variable.
    2) Original instructions use `OnInitAsync()`, but that is deprecated.  use `OnInitializedAsync()` instead.
    3) Always received NullReferenceException errors when trying to called during initialization.  
    4) Switched to a clickable button.
    5) Below `Welcome to your new app.`  Add the following code:
    
        ```
        <br />
        <button class="btn btn-primary" type="button" @onclick="GetMyLocation">Get My Location</button>
        <h3>You are here</h3>

        <div>
            Lat: @location?.Latitude <br />
            Long: @location?.Longitude <br />
            Accuracy: @location?.Accuracy <br />
        </div>

        @functions
        {
            Location location;

            //protected override async Task OnInitializedAsync()
            //{
            //    location = await locationService.GetLocationAsync();
            //}

            private async Task GetMyLocation()
            {
                location = await  locationService.GetLocationAsync();
            }
        }
        ```
1) Edit _Host.cshtml
   1) Add the script entry to __Host.cshtml_ to look as follows:
        ```
       <script src="_framework/blazor.server.js"></script>
       <script src="Location.js"></script>
       ```
   1)  The application will compile and run, but silently fails.
       Bring up the browser developer consoler (e.g. F12), and Console.
        There should be an error message about `Could not find 'AspNetMonsters' in 'window'.`  
        _Location.js_ has not been loaded.  This means that _Location.js_ is not being found.  
        A simple workaround is to copy the _Location.js_ file from the source code tree (or from GitHub)
        into a file under _<Project_Name>.wwwroot.Location.js_.  Copy the file now.
    1) Run the application.
       1) Browser will ask permission to know your location.  Click on __Allow__ or __Yes_
       2) Lat, Long and Accuracy will be displayed.

Success!

## Prebuilt
1) Download BlazorGeo from Github
2) 1) Install AspNetMonsters.Blazor.Geolocation
   1) Once the application is built, go to Package Manager Console and enter
   
    ```
    Install-Package AspNetMonsters.Blazor.Geolocation -IncludePrerelease
    ```
    2) -IncludePrerelease is required.
3) Compile and run.  
