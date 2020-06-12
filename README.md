# Java Exception
> Exceptions are disruptions to the ordinary flow of the application.

> Spring Boot has a default way of handling exceptions. 
* To provide some custom error messages when our program encounters an error, some custom exception handlers below.

> Two types of exceptions

**System generated.**  Exception.

> Exception that client needs to know.
* What is the exception
* What caused the exception
* What time the exception happened
* Any data types that are incorrect or missing
* Among other information that might vary by exception type.

**Set up our Models**
> Add a model called **ErrorDetail** to report exception. Do note that this is NOT annotated as an Entity. The data for this model will never appear in the database. This is just a POJO we are using internally in our application. 

```java
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class ErrorDetail
{
    private String title;

    private int status;
    private String detail;
    private Date timestamp;
    private String developerMessage;
    private List<ValidationError> errors = new ArrayList<>();

    public ErrorDetail()
    {
    }

    public String getTitle()
    {
        return title;
    }

    public void setTitle(String title)
    {
        this.title = title;
    }

    public int getStatus()
    {
        return status;
    }

    public void setStatus(int status)
    {
        this.status = status;
    }

    public String getDetail()
    {
        return detail;
    }

    public void setDetail(String detail)
    {
        this.detail = detail;
    }

    public Date getTimestamp()
    {
        return timestamp;
    }

    public void setTimestamp(Date timestamp)
    {
        this.timestamp = timestamp;
    }

    public String getDeveloperMessage()
    {
        return developerMessage;
    }

    public void setDeveloperMessage(String developerMessage)
    {
        this.developerMessage = developerMessage;
    }

    public List<ValidationError> getErrors()
    {
        return errors;
    }

    public void setErrors(List<ValidationError> errors)
    {
        this.errors = errors;
    }
}
```
**ErrorDetail references ValidationError** 
> This is a class we create to hold any data validation errors that might arise. This is NOT the ValidationErrors from the Spring Framework so don’t mistakenly import that class!

> Add the ValidationError models. Do note that this is NOT annotated as an Entity. The data for this model will never appear in the database. This is just a POJO we are using internally in our application.
```java
public class ValidationError
{
    private String Code;
    private String message;

    public String getCode()
    {
        return Code;
    }

    public void setCode(String code)
    {
        Code = code;
    }

    public String getMessage()
    {
        return message;
    }

    public void setMessage(String message)
    {
        this.message = message;
    }

    @Override
    public String toString()
    {
        return "ValidationError{" + "Code='" + Code + '\'' + ", message='" + message + '\'' + '}';
    }
}
```
**Turn off Default Exception Handling**
> Now we need to turn off the default exception handling of the Spring Framework. We will do this through an application property. However, after turning it off we need to handle manually all exceptions and reporting the information to the client that we want to report.

**Application Properities**
> Turn off default exception handling including handling incorrect endpoints. Specifically this will alway us to custom that default exception handling. The default exception handling is still present but uses our own customizations. We also need to turn static resource handling to capture unknown endpoints. So add the following lines to the application.properties file
```java
### Turns off Spring Boot automatic exception handling
server.error.whitelabel.enabled=false
spring.mvc.throw-exception-if-no-handler-found=true
spring.resources.add-mappings=false
```
**Mapping for Swagger**
> Since we have turned off static resource handling, we need to manually add the static paths for Swagger, the only valid static paths in our application. 
> Add a class under config called **SwaggerWebMVC**. This is boilerplate code that needs to be added to any application that uses custom exception handling and Swagger.
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * The application turns off any automatic web page generate done by Spring. This is done to improve exception handling.
 * However, we do need some web page generate done for Swagger, so we do that here.
 */
@Configuration
public class SwaggerWebMVC implements WebMvcConfigurer
{
    /**
     * Adds the Swagger web pages to Spring.
     * This still gives the following warning
     * <p>
     * No mapping for GET /
     * No mapping for GET /csrf
     * <p>
     * All works though
     *
     * @param registry the place that holds the web pages for Spring
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry)
    {
        registry.addResourceHandler("swagger-ui.html")
            .addResourceLocations("classpath:/META-INF/resources/");

        registry.addResourceHandler("/webjars/**")
            .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```
**Manual /error endpoint handling**
> We have turned off default error handling. Now let’s handle the exceptions. Some errors get routed to the endpoint /error. We need to change what /error reports to the client to our format.

> Create a new subpackage under sampleemps called exceptions. 
> Under that subpackge create a class called **CustomErrorDetails**. If future applications, you would modify what gets reported here to match what is in the ErrorDetail class. That way all our exceptions will report the same format of messages! Be careful that you import the right class for DefaultErrorAttributes!!!
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.web.servlet.error.DefaultErrorAttributes;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.WebRequest;

import java.util.LinkedHashMap;
import java.util.Map;

@Component
public class CustomErrorDetails
        extends DefaultErrorAttributes
{
    @Autowired
    private HelperFunctions helper;

    @Override
    public Map<String, Object> getErrorAttributes(
            WebRequest webRequest,
            boolean includeStackTrace)
    {

        //Get all the normal error information
        Map<String, Object> errorAttributes =
                super.getErrorAttributes(webRequest, includeStackTrace);
        // Linked HashMaps maintain the order the items are inserted. I am using it here so that the error JSON
        // produced from this class lists the attributes in the same order as other classes.
        Map<String, Object> errorDetails = new LinkedHashMap<>();
        errorDetails.put("title", errorAttributes.get("error"));
        errorDetails.put("status", errorAttributes.get("status"));
        errorDetails.put("detail", errorAttributes.get("message"));
        errorDetails.put("timestamp", errorAttributes.get("timestamp"));
        errorDetails.put("developerMessage", "path: " + errorAttributes.get("path"));

        errorDetails.put("errors", helper.getConstraintViolation(this.getError(webRequest)));
        return errorDetails;
    }
}
```
> This class uses a **HelperFunction** class. This class contains helpful methods that are used throughout the application. We will create a method that populates the validation constraint errors. We will use such a function in a few places throughout the application. 
> Create a new subpackage under sampleemps called handlers and create a class in that subpackage called HelperFunctions. 
```java
import com.lambdaschool.sampleemps.models.ValidationError;
import org.springframework.stereotype.Component;

import javax.validation.ConstraintViolation;
import javax.validation.ConstraintViolationException;
import java.util.ArrayList;
import java.util.List;

@Component
public class HelperFunctions
{
    public List<ValidationError> getConstraintViolation(Throwable cause)
    {
        // Find any data violations that might be associated with the error and report them
        // data validations get wrapped in other exceptions as we work through the Spring
        // exception chain. Hence we have to search the entire Spring Exception Stack
        // to see if we have any violation constraints.
        while ((cause != null) && !(cause instanceof ConstraintViolationException))
        {
            cause = cause.getCause();
        }

        List<ValidationError> listVE = new ArrayList<>();

        // we know that cause either null or an instance of ConstraintViolationException
        if (cause != null)
        {
            ConstraintViolationException ex = (ConstraintViolationException) cause;
            for (ConstraintViolation cv : ex.getConstraintViolations())
            {
                ValidationError newVe = new ValidationError();
                newVe.setCode(cv.getInvalidValue()
                    .toString());
                newVe.setMessage(cv.getMessage());
                listVE.add(newVe);
            }
        }
        return listVE;
    }
}
```
Run the application and surf to these endpoints to see what exception handling now looks like. You can expand each endpoint to see its output
```java
Wrong Data Type: http://localhost:2019/employees/employee/lambda

{
    "title": "Bad Request",
    "status": 400,
    "detail": "Failed to convert value of type 'java.lang.String' to required type 'long'; nested exception is java.lang.NumberFormatException: For input string: \"lambda\"",
    "timestamp": "2020-05-20 17:41:56",
    "developerMessage": "path: /employees/employee/lambda",
    "errors": []
}
Employee Not Found: http://localhost:2019/employees/employee/9999

{
    "title": "Internal Server Error",
    "status": 500,
    "detail": "Employee id 9999 not found",
    "timestamp": "2020-05-20 17:43:15",
    "developerMessage": "path: /employees/employee/9999",
    "errors": []
}
Invalid endpoint: http://localhost:2019/employees/turtle

{
    "title": "Not Found",
    "status": 404,
    "detail": "No message available",
    "timestamp": "2020-05-20 17:44:15",
    "developerMessage": "path: /employees/turtle",
    "errors": []
}
```
> There is nothing wrong with this output but we can still do better. We get more specific about each type of error.

**Unpredicted Exceptions**
> Let’s change the message that get returned when we encounter an exception we have not handled, a catch all for all exceptions! Under the handlers subpackage, add a class called **RestExceptionHandler**. This class extends the abstract class ResponseEntityExceptionHandler. We will override some methods from this abstract while leaving others at their default implementation.

> Note the RestControllerAdvice annotation. When an exception is encountered during a Rest Controller operation, that Rest Controller seeks advice from an advice class on how to handle that exception. If no advice is found, it uses the default exception handling built into Spring including the modifications we have made to that default exception handling.

We do use the helper function to fill in data validation constraint exceptions so need to autowire that in!
```java
import com.lambdaschool.sampleemps.models.ErrorDetail;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;

import java.util.Date;

/**
 * This is the driving class when an exception occurs. All exceptions are handled here.
 * This class is shared across all controllers due to the annotation RestControllerAdvice;
 * this class gives advice to all controllers on how to handle exceptions.
 * Due to the annotation Order(Ordered.HIGHEST_PRECEDENCE), this class takes precedence over all other controller advisors.
 */
@Order(Ordered.HIGHEST_PRECEDENCE)
@RestControllerAdvice
public class RestExceptionHandler
        extends ResponseEntityExceptionHandler
{
    @Autowired
    private HelperFunctions helper;

    /**
     * The constructor for the RestExceptionHandler. Currently we do not do anything special. We just call the parent constructor.
     */
    public RestExceptionHandler()
    {
        super();
    }

    @Override
    protected ResponseEntity<Object> handleExceptionInternal(
            Exception ex,
            Object body,
            HttpHeaders headers,
            HttpStatus status,
            WebRequest request)
    {
        ErrorDetail errorDetail = new ErrorDetail();
        errorDetail.setTimestamp(new Date());
        errorDetail.setStatus(status.value());
        errorDetail.setTitle("Rest Internal Exception");
        errorDetail.setDetail(ex.getMessage());
        errorDetail.setDeveloperMessage(ex.getClass()
                .getName());
        errorDetail.setErrors(helper.getConstraintViolation(ex));

        return new ResponseEntity<>(errorDetail,
                null,
                status);
    }
}
```
> Run the application and surf to these endpoints to see what exception handling now looks like. You can expand each endpoint to see its output
```java
Wrong data type: http://localhost:2019/employees/employee/lambda

{
    "title": "Rest Internal Exception",
    "status": 400,
    "detail": "Failed to convert value of type 'java.lang.String' to required type 'long'; nested exception is java.lang.NumberFormatException: For input string: \"lambda\"",
    "timestamp": "2020-05-20 18:03:05",
    "developerMessage": "org.springframework.web.method.annotation.MethodArgumentTypeMismatchException",
    "errors": []
}
Employee Not Found: http://localhost:2019/employees/employee/9999

{
    "title": "Internal Server Error",
    "status": 500,
    "detail": "Employee id 9999 not found",
    "timestamp": "2020-05-20 18:02:44",
    "developerMessage": "path: /employees/employee/9999",
    "errors": []
}
Invalid endpoint: http://localhost:2019/employees/turtle

{
    "title": "Not Found",
    "status": 404,
    "detail": "No message available",
    "timestamp": "2020-05-20 18:02:16",
    "developerMessage": "path: /employees/turtle",
    "errors": []
}
```

> What if we want to programmatically cause an exception? What if we want to stop the ordinary flow of our application and report that to our clients? To do this, we can throw an exception or use a try..catch statement. We have already thrown built-in exceptions. For example, look at the code for delete in the class EmployeeServiceImpl.
```java
    public void delete(long employeeid)
    {
        if (employeerepos.findById(employeeid)
            .isPresent())
        {
            employeerepos.deleteById(employeeid);
        } else
        {
            throw new EntityNotFoundException("Employee " + employeeid + " Not Found");
        }
    }
```
> Here we use throw new EntityNotFoundException("Employee " + employeeid + " Not Found"); which throws the built-in exception EntityNotFoundException.

In the method findEmployeeById we call a method called .orElseThrow which throws the EntityNotFoundException as well.
```java
    public Employee findEmployeeById(long id) throws
            EntityNotFoundException
    {
        return employeerepos.findById(id)
                .orElseThrow(() -> new EntityNotFoundException("Employee id " + id + " not found"));
    }
```
Java also includes the Try..Catch statement. This statement tries to run the code in the body. If all works, great! If not an exception is thrown, either by us, Java, or the Spring Framework, and the catch part gets executed. You can even have a finally part that will get executed no matter what.

try
{
    // the statements to try
} catch (Exception e)
{
    // do this is something went wrong
} finally
{
    // no matter what, do these statements
}

> Under the subpackage exceptions, create a class called ResourceNotFoundException. 
```java
public class ResourceNotFoundException
    extends RuntimeException
{
    public ResourceNotFoundException(String message)
    {
        super("Error from a Lambda School Application " + message);
    }
}
```
> The difference between this exception and the built-in exception EntityNotFoundException is the name and that the error message will contain the phrase “Error from a Lambda School Application”. Really, I am just showing this is possible!

> Now let’s handle this custom exception. Add the following code to the bottom of the RestExceptionHandler class under the subpackage handlers.
```java
    // annotation to say the following method is meant to handle any time the ResourceNotFoundException is thrown
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<?> handleResourceNotFoundException(ResourceNotFoundException rnfe)
    {
        ErrorDetail errorDetail = new ErrorDetail();
        errorDetail.setTimestamp(new Date());
        errorDetail.setStatus(HttpStatus.NOT_FOUND.value());
        errorDetail.setTitle("Resource Not Found");
        errorDetail.setDetail(rnfe.getMessage());
        errorDetail.setDeveloperMessage(rnfe.getClass()
            .getName());
        errorDetail.setErrors(helper.getConstraintViolation(rnfe));

        return new ResponseEntity<>(errorDetail,
            null,
            HttpStatus.NOT_FOUND);
    }
```
> Replace EntityNotFoundException with ResourceNotFoundException in EmployeeServiceImpl in order to return
the messages we want to the client in our ErrorDetail model.

> Surf to the following endpoint to see the new exception messages.
```java
Resource Not Found: http://localhost:2019/employees/employee/9999

{
    "title": "Resource Not Found",
    "status": 404,
    "detail": "Error from a Lambda School Application Employee id 9999 not found",
    "timestamp": "2020-05-20 19:58:39",
    "developerMessage": "com.lambdaschool.sampleemps.exceptions.ResourceNotFoundException",
    "errors": []
}
```
**Try..Catch statements**
> Read from an environment variable on our local system. we use a system method System.getenv().

> Change the main class, SampleempsApplication, to try and read in an environment variable called LAMBDA. So the main class will look like this in the end.
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@SpringBootApplication
public class SampleempsApplication
{
    public static void main(String[] args)
    {
        try
        {
            System.out.println(System.getenv("PATH").toUpperCase());
        } catch (Exception e)
        {
            System.out.println("No Such Environment Variable");
        } finally
        {
            SpringApplication.run(SampleempsApplication.class,
                    args);
        }
    }
}
```


> Java is strongly typed. 
* Use the @Column annotation to say if a column is required or must be unique across records.
* Use setters to convert or validate our data before it goes to the database.
* the import javax.validation.constraints are put right before the fields in the models whose values we want to restrict. 
```java
@Email(message = "must be a valid email") forces the field to be in a valid email format (username@domain.toplevel). If not, a message for “must be a valid email” is returned.
@Size(min = ??, max = ??) restricts the size of a String field.
@Min(value = ??) minimum value of an integer. Often used in conjunction with @Max
@Max(value = ??) maximum value of an integer. Often used in conjunction with @Min
@DecimalMin(value = "??.??") minimum value for doubles
@DecimalMax(value = "??.??") maximum value for doubles
@Postive must be a positive number
@Negative must be a negative number
@NotNull another way to validate whether a field can be null or not
@Future date must be in the future
@Pattern(value = "regex") allows you specific a regex pattern to use to validate the data! 
```
> In the Employee Model, right before the salary field, add the appropriate annotations for a minimum salary of 100,000 and a maximum salary of 120,000. 
```java
// ...

    @Transient
    public boolean hasvalueforsalary = false;

    @DecimalMax(value = "120000.0")
    @DecimalMin(value = "100000.0")
    private double salary;

// ...
```
> Now run your application. In your console window you will see an error message similar to the following.
```java
Caused by: javax.persistence.RollbackException: Error while committing the transaction
    at org.hibernate.internal.ExceptionConverterImpl.convertCommitException(ExceptionConverterImpl.java:81) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.engine.transaction.internal.TransactionImpl.commit(TransactionImpl.java:104) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:534) ~[spring-orm-5.2.2.RELEASE.jar:5.2.2.RELEASE]
    ... 20 common frames omitted
Caused by: javax.validation.ConstraintViolationException: Validation failed for classes [com.lambdaschool.sampleemps.models.Employee] during persist time for groups [javax.validation.groups.Default, ]
List of constraint violations:[
    ConstraintViolationImpl{interpolatedMessage='must be greater than or equal to 100000.0', propertyPath=salary, rootBeanClass=class com.lambdaschool.sampleemps.models.Employee, messageTemplate='{javax.validation.constraints.DecimalMin.message}'}
]
    at org.hibernate.cfg.beanvalidation.BeanValidationEventListener.validate(BeanValidationEventListener.java:140) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    ...
 ```
  
> These annotations are a great way to further validate your data!
