1. When you create new .net core project in visual studio, you can check the sdks that are installed
    on your machine

2. We can specify the target framework in .csproj file, for this goal we use target framework monica
    (TFM): 

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
        </PropertyGroup>

in this example the netcoreapp3.1 is the TFM

3. another tag that can be used in the <PropertyGroup> is <AspNetCoreHostingModel>:
    
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
        </PropertyGroup>

    a. InProcess hosting means that we want to host our app internally, so we will use IIS server to request to our app.
        
    b. OutOfProcess hosting means that we want to host our app externally, so we use kestrel as a internall web server and use 
        IIs or Nginx or Apache as external web server. (This is the default one, if we remove from the .csproj file)

4. In .csproj we also defines our packages: 

        <ItemGroup>
            <PackageReference Include="<package name>" />
        </ItemGroup>

5. When we use InProcess hosting model in .csproj file, in the function of CreateDefaultBuilder() in the program.cs file
    we actually call useIIS() function

6. We can get internal server, with this line of code:
            System.Diagnostics.Process.GetCurrentProcess().ProcessName

7. In Development environmetn we can use launchSetting.json file for determining the settings, this file is same as project 
    properties when we right click on the project.

    {
        "iisSettings": {
            "windowsAuthentication": false, 
            "anonymousAuthentication": true, 
            "iisExpress": {
            "applicationUrl": "http://localhost:10807",
            "sslPort": 44351
            }
        },
        "profiles": {
                "IIS Express": {
                "commandName": "IISExpress",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                }
            },
            "firstProject": {
                "commandName": "Project",
                "launchBrowser": true,
                "applicationUrl": "https://localhost:5001;http://localhost:5000",
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                }
            }
        }
    }

[ATTENTION]
8. the profile that we select, determine the server that we want to use, pay attention to the logic: 

    if we use Project as a server, we select the kestrel server and kestrel can be used only internally, so the hosting 
    model in the .csproj file will be ignored.
    if we use iis, iis can be used internally or externally, so based on the hosting model in the .csproj file, iis will be 
    used internally or externally.

9. We can use Configuration values that we store in appsettings.json file or environment variables by using instance of 
    IConfiguration, to use this instance we have to inject it to the constructor of startup class: 

    private readonly IConfiguration _config;
    public Startup(IConfiguration config) 
    {
        _config = config;
    }

and we can use it like this: 
    _config[""]

10. The order of the configuration value in the CreateDefaultBuilder() function by default is like this: 

        a. appsettings.json
        b. appsettings.{Environment}.json
        c. user secrets
        d. environment variables
        e. command-line arguments (to use this: dotnet run myKey = "")

11. Every middleware in asp.net core is the extension method for IApplicationBuilder:


        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>                  // this is requestDelegate, and context is httpContext object
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }

[ATTENTION] in this function app.UseEndpoints is the TERMINAL middleware, a terminal middleware is a middleware that not call 
    the next function, if you want to add another middleware that is not terminal middleware, use Use function: 

        app.Use(asyn (context, next) => 
        {
            logger.LogInformation("this is the request line");
            await next();
            logger.LogInformation("this is the response line");                 // every logic after next is in response line
        })


12. app.UseStaticFiles() server the static files from wwwroot folder.

13. Pay attention to the UseDeveloperExceptionPage, there are lot of information in this page

[ATTENTION]
14. always remeber most of the middlewares has the options parameter that we can use it to customize
    the middleware functionality, get this object with intelisense.

15. public void Configure(IApplicationBuilder app, IWebHostEnvironment env)

    IWebHostEnvironment takes it name from the ASPNETCORE_ENVIRONMENT variable in launchSetting.json
    file, and we can access it with env.EnvironmentName.

    the default value for ASPNETCORE_ENVIRONMENT is Production. and we also can set this environment
    variables in the operating system variables.

16. If you use MVC services by adding it to the ConfigureServices, if the mvc not finding the route, it will go to the next middleware:

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc(option => option.EnableEndpointRouting = false);
        }

17. We can return Json from a action by defining JsonResult:

        public JsonResult Index()
        {
            return Json(new { id=1, name="alireza"});
        }

18. There are some differences between addMvc and addMvcCore that we use in ConfigureServices method, addMvcCore only
    uses minimum essential services, for authorization, formatter, and validation we should use addMvc:

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc(option => option.EnableEndpointRouting = false);
        }

19. The best way to make a property in visual studio is to type prop and press the tab twice

20. if we use ViewData[] for string type we can add it to variable but if we use for another data type we have to use
    as keyword: (ViewBag does not need type casting with as keyword)

        var name = ViewData["name"];
        var e1 = ViewData["Employee"] as Employee;

21. The problem with ViewBag is that, the type is dynamic and we don't get compile time error or get the intelisense.

22. If you don't define model type in razor view, @Model is dynamic type, so you have to use @model <type> to make 
    a Model variable static type.

23. ViewModels are used to transfer data between controllers and views, for this reason they are called data transfer objects
    or DTO

24. If you use layout in the app you have to specify the layout for each razor page, or use :

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
    }

25. You can include a refference to a script file, simply by draging a script file to the page.

26. Just like @RenderBody() function that we use in layout, there is another function with the name @RenderSection(string name)

27. There is overload to this function, that we can use required parameter:

        @RenderSection("Scripts", required: false)

28. Or we can use another function:

    @if(IsSectionDefined("Scripts"))
    {
        RenderSection("Scripts", required: true);
    }

29. To defince a section we use @section directive in the razor page:

    @section <name of the section> {

    }

30. we can use _ViewStart.cshtml run before any views run. we normally place this file directly in the Views folder.

[ATTENTION]
31. we can have many _ViewStart.cshtml file, and we can place them in any folder in the Views folder, and these new files
    will override the parent _ViewStart.cshtml file

32. If we use Layout = _Layout in _ViewStart.cshtml file, we can still override the Layout in the razor page itself

33. we can add _ViewImports.cshtml in our Views folder to add the namespaces that we commonly use in our Views files,
    the _ViewImports.cshtml files runs befor any Views files run. for example we can put:

        @using <projectname>.ViewModels; 
        @using <projectname>.Models;

        by using this directive you can import from ViewModels and Models folder in all your razor pages.

[ATTENTION]
34. just like _ViewStart.cshtml file, we can add another _ViewImports.cshtml file in a specific folder and we also can 
    override the things in this file, in the razor page itself.

[ATTENTION]
35. In attribute routing if you use ~/ or / in the address the parameter in the control routing will be ignored:

        namespace firstProject.Controllers
        {
            [Route("Home")]
            public class HomeController : Controller
            {
                private readonly IEmployeeRepository _employeeRepository;

                public HomeController(IEmployeeRepository employeeRepository)
                {
                    _employeeRepository = employeeRepository;
                }
                [HttpGet("")]
                [HttpGet("Index")]
                [HttpGet("~/")]
                public ViewResult Index()
                {
                    var model = _employeeRepository.GetAllEmployees();
                    return View(model);
                }
                [HttpGet("Detail/{id?}")]
                public ViewResult Detail(int? id)
                {
                    firstProject.ViewModels.Home.Detail viewModel = new ViewModels.Home.Detail()
                    {
                        Employee = _employeeRepository.GetEmployee(id ?? 1)
                    };
                    return View(viewModel);
                }
            }
        }

36. in attribute routing we can use token replacement: 

                namespace firstProject.Controllers
                {
                    [Route("[controller]/[action]")]
                    public class HomeController : Controller
                    {
                        private readonly IEmployeeRepository _employeeRepository;

                        public HomeController(IEmployeeRepository employeeRepository)
                        {
                            _employeeRepository = employeeRepository;
                        }
                        [HttpGet("")]
                        [HttpGet("~/")]
                        public ViewResult Index()
                        {
                            var model = _employeeRepository.GetAllEmployees();
                            return View(model);
                        }
                        [HttpGet("{id?}")]
                        public ViewResult Detail(int? id)
                        {
                            firstProject.ViewModels.Home.Detail viewModel = new ViewModels.Home.Detail()
                            {
                                Employee = _employeeRepository.GetEmployee(id ?? 1)
                            };
                            return View(viewModel);
                        }
                    }
                }

37. libman is a client side library manager. we can use it in visual studio by UI or editting the lib.json file.
    in visual studio mac we can add libman by going to  visual studio menu /Extensions/
    in gallery tab, expand IDE extensions and install Library manager.

38. image tag helper in asp.net core: 
    
    if you change the content of the image, but don't change the image name, browser cache will show the old picture, but if you use 
    asp-append-version="true" this problem will be solved: 

        <img src="~/images/imagename.jpg" asp-append-version="true" />

        asp-append-version give a unique string to the address the image. (you can see this in the page source)

[ATTENTION]
39. you can use asp-append-version also in js and static files.

40. using Environment tag helpers to load the different files for different environments:

    <environment include="Development">
        <link href="~/css/bootstrap.min.css" rel="stylesheet">
    </environment>

    <environment include="Production, Staging">
        <link href="https://CDN URL/css/bootstrap.min.css">
    </environment>

[ATTENTION]
41. instead of include, you can also use exclude, to exclude certain environment.

[ATTENTION]
42. When you get something from a CDN, for example a css file, there is integrity attribute in it, integrity uses hash code for 
    security, you can also define fallback using asp tag helper, if there is problem with downloading from CDN:

        <environment exclude="Development">
            <link rel="stylesheet"
                href="https://CDN/css/..."
                asp-fallback-href=""
                asp-fallback-test-class="sr-only"
                asp-fallback-test-property="position"
                asp-fallback-test-value="absolute"
                asp-suppress-fallback-integrity="true">
        </environment>

43. We can use form tag helpers in asp.net core: 
        if you want to use form tag helper you must define the model type for the view

        a. the first two tag helpers used in form element is asp-controller and asp-action: 

                <form asp-controller="" asp-action=""></form>

        b. the second tag helper is asp-for that used for input and label:

                <label asp-for="Name"></label>
                <input asp-for="Name"></input>

        c. the third tag helper is asp-items that we can use with the select element: 
            
            <label asp-for="Department"></label>
            <select asp-for="Department" asp-items="Html.GetEnumSelectList<Dept>()"/>

            Dept is the enum, and you have to change the Department type in model
            to enum


44. we can use asp-validation-for tag helper, to show the error of the field of viewModel for the form: 

        <div class="form-group row">
            <label asp-for="Email" class="col-2 col-form-label"></label>
            <div class="col-10">
                <input asp-for="Email" class="form-control" placeholder="Email" />
                <span asp-validation-for="Email" class="text-danger"></span>
            </div>
        </div>

45. There are many validation attributes that can be used for your view model: 

        RegularExpression
        Required
        Range
        MinLength
        MaxLength
        Compare

46. You can also define custom error message for a attribute: 

        [Required(ErrorMessage = "The field must be present")]

47. By default the label for the input field will be same as the name of the field in viewModel, you can change this by Display 
    attribute:

            public class Employee
            {
                public int Id { get; set; }
                [Required]
                public string Name { get; set; }
                [Required]
                [EmailAddress(ErrorMessage = "Invalid Email address")]
                [Display(Name = "Office Email")]
                public string Email { get; set; }
                public Dept Department { get; set; }
            }

48. We can display the summary of errors with asp-validation-summary: 

            <div asp-validation-summary="All"></div>        (we can use ModelOnly, None instead of All)

49. We have to check for the validation of model in action method: 

        public IActionResult Create(Employee employee) 
        {
            if(ModelState.IsValid)
            {
                // create the employee
                return RedirectToAction("Detail", new {id = employee.Id});
            }
            return View();  // by returning to view, we can use asp-validation-for and asp-validation-summary tag helpers to 
                            // show the errors that attach to ModelState in View
        }

50. If the model is not valid, you can show the view again and by using asp-validation-for and asp-validation-summary you can display 
    the errors.

51. If you use a enum for your view model and bind this view model to the form by using form tag helpers, the field for enum become
    required, because every datatype like integer (enum underlying is just integer), float, and ... are required by default.
    you can make it optional by making it nullable(using ? for the field), and after that you can use [Required] attribute and show
    custom error message

52. EF core supports code first approach and DB first approach but, the support for DB first approach is very limited.

53. EF core uses domain classes (classes in the application) and some classes from the EF core (db context classes), to make the DB and
    required tables in code first approach. EF core uses convention for creating the DB and tables.

54. In DB first approach the EF core makes the DB context classes and domain classes based on the existing DB.

55. EF core uses special library for each DB type, and we can install these libraries (Database providers) by using NuGet package manager
    this library act as a middle layer between the EF core and the target Database.

56. To use EF core with SQL server we need to install 3 packages: 

        a. Microsoft.EntityFrameworkCore.SqlServer  (this is the specific package for sql server)

        b. Microsoft.EntityFrameworkCore.relational     (this is the package that is common for relational databases)

        c. Microsoft.EntityFrameworkCore                (This is the core functionality of EF core)

[ATTENTION] If you install the SqlServer package first, because this package has dependency to other packages, the other packages will
            be installed automatically.

57. DB context class is a class that is responsible for making DB, tables, and ..., to use DbContext class you have to define your custom
    classes that derives from DbContext class. you can use your domain classes in the DbContext class, to make your db.

58. To configure the custom DbContext class you need to use DbContextOptions class, this class will be used for configuration such as 
    connection string, database providers, and ... . you can pass this class with dependency injection to the DbContext class.

        public class AppDbContext : DbContext
        {
            public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
            {

            }    
        }

59. Then you can use DbSet class in AppDbContext to define the structure of your database:

        public class AppDbContext : DbContext
        {
            public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
            {

            }
            public DbSet<Employee> Employees {get; set;}    
        }

