# EnterpriseApplication

**SOLID**
-   Single-responsibility principle (SRP):
-   Open-closed principle (OCP): open for extension but closed for modification. This means the regression testing of existing behaviors can be avoided; only extensions need to be tested
-   Liskov substitution principle (LSP): Parent or base class instances should be replaceable with instances of their derived classes or subtypes without altering the
sanity of the program
-   Interface segregation principle (ISP):  Instead of one common large interface, you should plan multiple, scenario-specific interfaces for better decoupling and change management:
-   Dependency inversion principle: You should avoid having any direct dependency on concrete implementations.

**Don't Repeat Yourself (DRY)**

**Keep it simple, stupid (KISS)**

**Design patterns**
-   Creative: Helpful in creating objects
-   Structural: Helpful in dealing with the composition of objects
-   Behavioral: Helpful in defining the interactions between objects and distributing responsibility

**common enterprise architectures**
Common issues with monolithic apps
-   the only way to horizontally scale is by adding more compute to the system
-   As all the features mostly use single storage, there is the possibility of locks leading to high latency

list of issues associated with availability, reliability, and performance
-   Any changes in the system will require the redeployment of all components, leading to downtime and low availability
-   Any non-persistent state, such as sessions stored in a web app, will be lost after every deployment
-   Any bugs in a module, such as memory leaks or security bugs, make all the modules vulnerable and have the potential to impact the whole system
-   Due to the highly coupled nature and sharing of resources within modules, there will always be unoptimized use of resources, leading to high latency in the system

**impact on the business and engineering teams**
-   The impact of a change is difficult to quantify and requires extensive testing. Hence, it slows down the rate of delivery to production. Even a small change will require the entire system to be deployed again
-   In a single highly coupled system, there will always be physical limits on collaborations across teams to deliver any features
-   New scenarios such as mobile apps, chatbots, and analysis engines will take more effort as there are no independent reusable components or services
-   Continuous deployment is almost impossible

**solve these common problems by adopting some proven principles/ architectures**

Separation of concerns/single-responsibility architecture

-   N-tier architecture
-   Microservices architecture
-   Stateless services architecture
-   Event-driven architecture 
    -   In an event-driven architecture, communication, which is generally known as publisher-subscriber communication, between modules, is primarily asynchronous and achieved via events. Producers and consumers are totally decoupled from each other. The structure of the event is the only contract that is exchanged between them
    -   There can be multiple consumers of the same event taking care of their specific operations; ideally, they won't even be aware of each other. Producers can continuously push events without worrying about the availability of consumers
    -   Publishers publish events via a messaging infrastructure such as queues or a service bus. Once an event has been published, the messaging infrastructure is responsible for sending the event to eligible subscribers.
-   Resiliency architecture
    -    applications, services, and data should be replicated globally in at least two Azure regions for planned downtime and unplanned transient or permanent failures
    Choosing Azure paired regions will help in business continuity and disaster recovery (BCDR)
    -   Transient faults can occur in any type of communication or service. need to have a strategy to recover from transient faults,
        -   Identify the operation and type of transient fault. Then, determine the appropriate retry count and interval
        -   Avoid anti-patterns such as endless retry mechanisms with a finite number of retries or circuit breakers.
    -   If a failure is not transient, you should respond to the failure gracefully by choosing some of the following options
        -   Failing over
        -   Compensating for any failed operations
        -   Throttling/blocking the bad client/actor
        -   Using a leader election to select a leader in the case of a failure
    -   Here, telemetry plays a big role; you should have custom metrics to keep a tab on the health of any component. Alerts can be raised when a custom event occurs or a specific metric reaches a certain threshold.


# .net 6

    dotnet --version

    ## targeting .NET Standard 2.0
    dotnet new classlib -o MyLibrary -f netstandard2.0

    ## target multiple frameworks
        <TargetFrameworks>net6.0;netstandard2.0 </TargetFrameworks>
    
When you configure your application to support multiple frameworks and build the project, you will notice it creates artifacts for each targeted framework version

-   Use .NET Standard 2.0 to share code between .NET Framework and all other platforms.
-   Use .NET Standard 2.1 to share code between Mono, Xamarin, and .NET Core 3.x.
-   Use .NET 6 for code sharing moving forward.

## Understanding .NET 6 cross-platform and cloud application support

-   NET Framework
-   .NET 6
-   Universal Windows Platform (UWP)
-   Mono    

### Cloud support
    -   Azure App Service
    -   Azure Functions
    -   Docker

##   C# 10

-   Simplification of the using directives
-   The record structs
-   Improvements to the Lambda expressions
-   Enhancements to interpolated strings
-   Extended property patterns
-   Additions to the caller argument attributes

### Simplification of the using directives

-   The global using directives

        global using System.Threading;
        global using SRS = System.Runtime.Serialization;
-   The implicit using directives : a few framework namespaces, such as System and System.Linq, are present in almost all the classes. With C# 10, these commonly-used namespaces are implicitly added as global using directives

In addition to these, if we wish to include any other namespace to be part of these implicit directives or remove any of the predefined namespaces, we can do so by adding ItemGroup to the .csproj file, as shown here:

    <ItemGroup>
        <Using Include="System.Threading" />
        <Using Remove="System.IO" />
    </ItemGroup>

To completely remove the implicit using directives, we can uncheck the Implicit global usings option from the project properties under the Build | General tab, or by disabling the ImplicitUsings flag in the .csproj file as shown here:

    <ImplicitUsings>disable</ImplicitUsings>

### The record structs

The record types introduced in C# 9, C# 10 brings us the record structs, it is recommended to specify class or struct explicitly for better readability

We use the record keyword to declare record class, and we use the same record keyword to declare record struct, as shown in the following code:

    public record struct Employee(string Name);

    public readonly record struct Employee(string Name);

To change the fields of the readonly record struct, we can use the operator as shown here like with the record class:

    Employee employee2 = employee1 with { LastName = string.Empty};

### Improvements to Lambda expressions

-   Inferring the expression type 
-   Return types for Lambda expressions

    the compiler cannot infer the return type, which will result in a compilation error

        var createExpression = (bool condition) => condition ? new Employee() : new Manager();

    With C#10, we can now explicitly specify the return type for a Lambda expression as shown in the following code

        var createEmployee = Person (bool hasReportees) => condition ? new Manager() : new Employee();
        // Create the Person object based on condition
        var manager = createEmployee(true);
-   Adding attributes to Lambda expressions

    Starting from C# 10, we can add attributes to the Lambda expressions and their parameters and return types. The following code snippet defines a Lambda expression to retrieve employees for the given ID:     

        var GetEmployeeById =  [Authorize] Employee ([FromRoute]int id) => { return new Employee { }; };
    
    The GetEmployeeeById expression has the [Authorize] attribute and the id parameter is attributed with [FromRoute]

    Attributes on Lambda expressions don't have any effect when they are invoked, as the invocation is via the underlying delegate type. The attributes defined on Lambda expressions can be discovered via reflection

### Enhancements to interpolated strings

C# 6 added a new language syntax called interpolated strings

    string messageWithVersion = $"{Greeting}, {Language} {version}!";

 C# 10 compiler now uses interpolated string handlers, The built-in interpolated string handler in C# 10 is **DefaultInterpolatedStringHandler**. 

 ### Extended property patterns

 Pattern matching is a way to check an object's value or the value of a property having a full or partial match to a sequence

        // // Accessing the extended propers 
        if (product is Product { Location: { Country: "USA" } })     Console.WriteLine("USA");

        // Accessing the extended properties with additional support in C#10
        if (product is Product { Location.Country: "USA" })
            Console.WriteLine("USA");

###  Addition to the caller argument attributes

C# 5 first introduced caller argument attributes. They are CallerMemberName, CallerFilePath, and CallerLineNumber. These attributes make the compiler populate the method arguments in the generated code

**CallerArgumentExpression** is a new addition to these in C# 10

        static void AddToCart(Product product, uint quantity)
        {
            ArgumentValidation.ThrowIfNull(product);
            // Implementation to add the product to cart
        }