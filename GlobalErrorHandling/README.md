# Dependency
 Created by İBRAHİM MEŞE .NET8

 ## Install
 Kurulum için bu kodu kullanın

  ```Csharp
  public class ExampleBusinessRules(IExampleRepository exampleRepository):BaseBusinessRules
{
    public async Task ExampleNameCannotBeDupplicatedInserted(string name)
    {
        Example example = await exampleRepository.GetAsync(p=>p.Name.ToLower()==name.ToLower());
        if(example is not null )
        {
            throw new BusinessException("Name Is Exist");
        }
    }
}
  ```


  ```Csharp
  public static class ApplicationServiceRegistration
{
    public static IServiceCollection AddApplicationServices(this IServiceCollection services)
    {

        services.AddSubClassesOfType(Assembly.GetExecutingAssembly(),typeof(BaseBusinessRules));
        return services;
    }
     public static IServiceCollection AddSubClassesOfType(
       this IServiceCollection services,
       Assembly assembly,
       Type type,
       Func<IServiceCollection, Type, IServiceCollection>? addWithLifeCycle = null
   )
    {
        var types = assembly.GetTypes().Where(t => t.IsSubclassOf(type) && type != t).ToList();
        foreach (var item in types)
            if (addWithLifeCycle == null)
                services.AddScoped(item);
            else
                addWithLifeCycle(services, type);
        return services;
    }
}
  
  ```

  
  ```Csharp
  //Use
   await exampleBusinessRules.ExampleNameCannotBeDupplicatedInserted(request.Name);
   ```
     

   ```Csharp
       if (app.Environment.IsProduction())
            {
                app.ConfigureCustomExceptionMiddleware();
            }
   ```

 
 
 
 ```Csharp
 //UseValidationException
  services.AddSubClassesOfType(Assembly.GetExecutingAssembly(),typeof(BaseBusinessRules));
  ```

  ```Csharp
  services.AddMediatR(configuration =>
        {
            configuration.RegisterServicesFromAssembly(Assembly.GetExecutingAssembly());

            configuration.AddOpenBehavior(typeof(RequestValidationBehavior<,>));
        });
        return services;
  ```

   ```Csharp
  public class CreateExampleCommandValidator:AbstractValidator<CreateExampleCommand>
{
    public CreateExampleCommandValidator()
    {
        RuleFor(c=>c.Name).NotEmpty().MinimumLength(2);
    }
}
   ```


   
   ```Csharp
   //UseTransactionalRequest

     services.AddMediatR(configuration =>
        {
            configuration.RegisterServicesFromAssembly(Assembly.GetExecutingAssembly());

            configuration.AddOpenBehavior(typeof(TransactionScopeBehavior<,>));
        });
        return services;
   ```

   ```Csharp
   public class CreateExampleCommand() :IRequest<CreatedExampleResponse>, ITransactionalRequest
   ```