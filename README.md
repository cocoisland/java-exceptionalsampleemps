Exceptions are disruptions to the ordinary flow of the application. Thus when something happens that is an exception to the normal operation of your application, we have an exception! Exceptions can take on many forms

Application Error
The file, environment variable we seek is not found
The API tries doing an invalid math expression
The API loses accesses to the webserver
User Error
Looking for resources that are not available
Using an endpoint that does not exist
Sending the API the wrong data type
Aliens
Ok, maybe not aliens. However, there always seems to be some one off situation that causes a weird exception!
Spring Boot has a default way of handling exceptions. It does this fairly well. Up until now we have just used the Spring Boot defaults to handle our exceptions. What if we wanted to provide some custom error messages when our program encounters an error? What if we have custom ways we want to handle exceptions that differ from the Spring Boot Default? We will be coding some custom exception handlers below.

Our application experiences two types of exceptions

System generated. Something happened we were not excepting. The client sent us a String value when we were expecting an integer. These are handled in this objective.
Thrown exceptions. Something happened we knew might happen so we reported manually by “throwing” an exception. The client looked for a resource that was not available. These are handled in the objective on Java Exception Constructs.
When we are writing our custom exception handles we need to handle both types.

We also need a way to report information back to the client in a standard way. In this way, our client need only address one type of error message. We need to let the client know

What is the exception
What caused the exception
What time the exception happened
Any data types that are incorrect or missing
Among other information that might vary by exception type.
Terminology

When an exception happens, it is considered thrown. We throw a ResourceNotFound exception. The system throws a FileNotFound exception.
When we handle an exception, it is considered caught. We will catch any thrown exceptions!
So let’s “catch” all possible exceptions and report to the client what happened. Spring Boot will handle continuing the application causing it to wait for the next request!

Note that another reason we look at Exception Handling is here we really take advantage of annotations to control the flow of our programs and the object oriented ness of Java to make our code reusable. So pay particular attention to the flow the application throughout this objective!

Follow Along
Open up the application exceptionalsampleemp-initial from the GitHub Repository https://github.com/LambdaSchool/java-exceptionalsampleemps.git. This application is the same as the application from https://github.com/LambdaSchool/java-sampleemps.git/sampleemps_data_modeling with the addition of find employee by id endpoints and associated code.

Run the application and surf to these endpoints to see what default exception handling looks like. You can expand each endpoint to see its output

Wrong Data Type: http://localhost:2019/employees/employee/lambda

{
    "timestamp": "2020-05-20 17:32:14",
    "status": 400,
    "error": "Bad Request",
    "message": "Failed to convert value of type 'java.lang.String' to required type 'long'; nested exception is java.lang.NumberFormatException: For input string: \"lambda\"",
    "trace": "org.springframework.web.method.annotation.MethodArgumentTypeMismatchException: Failed to convert value of type 'java.lang.String' to required type 'long'; nested exception is java.lang.NumberFormatException: For input string: \"lambda\"\n\tat org.springframework.web.method.annotation.AbstractNamedValueMethodArgumentResolver.resolveArgument(AbstractNamedValueMethodArgumentResolver.java:133)\n\tat org.springframework.web.method.support.HandlerMethodArgumentResolverComposite.resolveArgument(HandlerMethodArgumentResolverComposite.java:121)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.getMethodArgumentValues(InvocableHandlerMethod.java:167)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:134)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:888)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:793)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1040)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:634)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:741)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:526)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:367)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:860)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1591)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:834)\nCaused by: java.lang.NumberFormatException: For input string: \"lambda\"\n\tat java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)\n\tat java.base/java.lang.Long.parseLong(Long.java:692)\n\tat java.base/java.lang.Long.valueOf(Long.java:1144)\n\tat org.springframework.util.NumberUtils.parseNumber(NumberUtils.java:214)\n\tat org.springframework.beans.propertyeditors.CustomNumberEditor.setAsText(CustomNumberEditor.java:115)\n\tat org.springframework.beans.TypeConverterDelegate.doConvertTextValue(TypeConverterDelegate.java:429)\n\tat org.springframework.beans.TypeConverterDelegate.doConvertValue(TypeConverterDelegate.java:402)\n\tat org.springframework.beans.TypeConverterDelegate.convertIfNecessary(TypeConverterDelegate.java:155)\n\tat org.springframework.beans.TypeConverterSupport.convertIfNecessary(TypeConverterSupport.java:73)\n\tat org.springframework.beans.TypeConverterSupport.convertIfNecessary(TypeConverterSupport.java:53)\n\tat org.springframework.validation.DataBinder.convertIfNecessary(DataBinder.java:693)\n\tat org.springframework.web.method.annotation.AbstractNamedValueMethodArgumentResolver.resolveArgument(AbstractNamedValueMethodArgumentResolver.java:125)\n\t... 47 more\n",
    "path": "/employees/employee/lambda"
}
Employee Not Found: http://localhost:2019/employees/employee/9999

{
    "timestamp": "2020-05-20 17:35:09",
    "status": 500,
    "error": "Internal Server Error",
    "message": "Employee id 9999 not found",
    "trace": "javax.persistence.EntityNotFoundException: Employee id 9999 not found\n\tat com.lambdaschool.sampleemps.services.EmployeeServiceImpl.lambda$findEmployeeById$0(EmployeeServiceImpl.java:35)\n\tat java.base/java.util.Optional.orElseThrow(Optional.java:408)\n\tat com.lambdaschool.sampleemps.services.EmployeeServiceImpl.findEmployeeById(EmployeeServiceImpl.java:35)\n\tat com.lambdaschool.sampleemps.services.EmployeeServiceImpl$$FastClassBySpringCGLIB$$4105708a.invoke(<generated>)\n\tat org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)\n\tat org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:769)\n\tat org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163)\n\tat org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:747)\n\tat org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:366)\n\tat org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:99)\n\tat org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)\n\tat org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:747)\n\tat org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:689)\n\tat com.lambdaschool.sampleemps.services.EmployeeServiceImpl$$EnhancerBySpringCGLIB$$b166ea5a.findEmployeeById(<generated>)\n\tat com.lambdaschool.sampleemps.controllers.EmployeeController.getEmployeeById(EmployeeController.java:37)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)\n\tat java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)\n\tat java.base/java.lang.reflect.Method.invoke(Method.java:566)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:138)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:888)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:793)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1040)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:634)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:741)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:526)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:367)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:860)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1591)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:834)\n",
    "path": "/employees/employee/9999"
}
Invalid endpoint: http://localhost:2019/employees/turtle

{
    "timestamp": "2020-05-20 17:35:38",
    "status": 404,
    "error": "Not Found",
    "message": "No message available",
    "path": "/employees/turtle"
}
There is nothing wrong with this output but we can do better, provide more meaningful output to our clients.

Set up our Models
Let’s add the models needed to report exception, error messages back to the client. You can modify these as you wish for your applications. However, for now, let’s use this code. Add a model called ErrorDetail using the following code. Do note that this is NOT annotated as an Entity. The data for this model will never appear in the database. This is just a POJO we are using internally in our application. Remember in adding all of this code, you will need to make sure the proper imports are done!

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
Notice that ErrorDetail references ValidationError. This is a class we create to hold any data validation errors that might arise. This is NOT the ValidationErrors from the Spring Framework so don’t mistakenly import that class!

Let’s now add the ValidationError models using the following code. Again, do note that this is NOT annotated as an Entity. The data for this model will never appear in the database. This is just a POJO we are using internally in our application.

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
Turn off Default Exception Handling
Now we need to turn off the default exception handling of the Spring Framework. We will do this through an application property. However, after turning it off we need to handle manually all exceptions and reporting the information to the client that we want to report.

Application Properities
Let’s add the appropriate code to application.properities. These turn off default exception handling including handling incorrect endpoints. Specifically this will alway us to custom that default exception handling. The default exception handling is still present but uses our own customizations. That is what we mean by “turn off” default exception handling. We also need to turn static resource handling to capture unknown endpoints. So add the following lines to the application.properties file

### Turns off Spring Boot automatic exception handling
server.error.whitelabel.enabled=false
spring.mvc.throw-exception-if-no-handler-found=true
spring.resources.add-mappings=false
Mapping for Swagger
Since we have turned off static resource handling, we need to manually add the static paths for Swagger, the only valid static paths in our application. Add a class under config called SwaggerWebMVC. Then add the following code to the class. Feel free not to add the comments! This is boilerplate code that needs to be added to any application that uses custom exception handling and Swagger.

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
Manual /error endpoint handling
We have turned off default error handling. Now let’s handle the exceptions. Some errors get routed to the endpoint /error. We need to change what /error reports to the client to our format.

Create a new subpackage under sampleemps called exceptions. Under that subpackge create a class called CustomErrorDetails. Then add the following code to that class. If future applications, you would modify what gets reported here to match what is in the ErrorDetail class. That way all our exceptions will report the same format of messages! Be careful that you import the right class for DefaultErrorAttributes!!!

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
This class uses a HelperFunction class. This class contains helpful methods that are used throughout the application. We will create a method that populates the validation constraint errors. We will use such a function in a few places throughout the application. Create a new subpackage under sampleemps called handlers. Create a class in that subpackage called HelperFunctions. Add the following code to that class.

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
Run the application and surf to these endpoints to see what exception handling now looks like. You can expand each endpoint to see its output

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
There is nothing wrong with this output but we can still do better. We get more specific about each type of error.

Unpredicted Exceptions
Let’s change the message that get returned when we encounter an exception we have not handled, a catch all for all exceptions! Under the handlers subpackage, add a class called RestExceptionHandler. Here we will put code to address specific types of errors. Add the following code to the class to better handle invalid endpoint exceptions. This class extends the abstract class ResponseEntityExceptionHandler. We will override some methods from this abstract while leaving others at their default implementation.

Note the RestControllerAdvice annotation. When an exception is encountered during a Rest Controller operation, that Rest Controller seeks advice from an advice class on how to handle that exception. If no advice is found, it uses the default exception handling built into Spring including the modifications we have made to that default exception handling.

We do use the helper function to fill in data validation constraint exceptions so need to autowire that in!

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
Run the application and surf to these endpoints to see what exception handling now looks like. You can expand each endpoint to see its output

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
Dig Deeper
Java Fundamentals Tutorial: Exceptions
Java Fundamentals Tutorial: Exceptions
Learn to implement exception handling using a variety of Java constructs

Overview
See the Github Repository https://github.com/LambdaSchool/java-exceptionalsampleemps.git for the code used in the objective.
Software Needed
Java Development Kit (JDK) - at least version 11
JetBrains IntelliJ IDEA IDE
Postman
What if we want to programmatically cause an exception? What if we want to stop the ordinary flow of our application and report that to our clients? To do this, we can throw an exception or use a try..catch statement. We have already thrown built-in exceptions. For example, look at the code for delete in the class EmployeeServiceImpl.

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
Here we use throw new EntityNotFoundException("Employee " + employeeid + " Not Found"); which throws the built-in exception EntityNotFoundException.

In the method findEmployeeById we call a method called .orElseThrow which throws the EntityNotFoundException as well.

    public Employee findEmployeeById(long id) throws
            EntityNotFoundException
    {
        return employeerepos.findById(id)
                .orElseThrow(() -> new EntityNotFoundException("Employee id " + id + " not found"));
    }
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
Notice that with catch we say which exception we wish to catch. Above we use Exception e to represent all exceptions. However, if we want to handle different exceptions differently, we can do that! In the following exception, we address an IOException, a SQLException and then all other exceptions differently.

try
{
    // the statements to try
} catch (SQLException e)
{
    System.out.println("Error processing database");
} catch (IOException e)
{
    System.out.println("We could not find your file");
} catch (Exception e)
{
    System.out.println("Ouch. That did not work!" + e);
} finally
{
    // no matter what, do these statements
}
Follow Along
Create our own exception
Let’s create our own exception that we can throw and look at how try..catch statements work in Java. Let’s add each example to our exceptionalsampleemps application we started in the previous objective.

If you haven’t already, open the application exceptionalsampleemps you worked on in the previous objective on Exception Handling. Let’s create our own exception we can throw. Under the subpackage exceptions, create a class called ResourceNotFoundException. This is the name of the exception we are going to create. Add the following code to that class. Remember in adding all of this code, you will need to make sure the proper imports are done!

public class ResourceNotFoundException
    extends RuntimeException
{
    public ResourceNotFoundException(String message)
    {
        super("Error from a Lambda School Application " + message);
    }
}
The difference between this exception and the built-in exception EntityNotFoundException is the name and that the error message will contain the phrase “Error from a Lambda School Application”. Really, I am just showing this is possible!

Now let’s handle this custom exception. Add the following code to the bottom of the RestExceptionHandler class under the subpackage handlers.

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
Thus we have an annotation saying which method will handle ResourceNotFoundExceptions. We put the messages we want to return to the client in our ErrorDetail model and then we return that object to the client. Now we have to throw the ResourceNotFoundException in our code instead of the EntityNotFoundException. So open the EmployeeServiceImpl and the class and do a replace, replacing EntityNotFoundException with ResourceNotFoundException. Do the same thing with JobTitleServiceImpl!

Hint to replace code, with the correct file selected,

go to Edit -> Find -> Replace.
In the find box, the first one, enter EntityNotFoundException
In the replace box, the second one, enter ResourceNotFoundException
Click on Replace all
Let’s see what happens now when we try to find an employee that is not there. Run the application exceptionalsampleemps. Surf to the following endpoint. Expand the endpoint to see its output.

Resource Not Found: http://localhost:2019/employees/employee/9999

{
    "title": "Resource Not Found",
    "status": 404,
    "detail": "Error from a Lambda School Application Employee id 9999 not found",
    "timestamp": "2020-05-20 19:58:39",
    "developerMessage": "com.lambdaschool.sampleemps.exceptions.ResourceNotFoundException",
    "errors": []
}
Try..Catch statements
To see how a try..catch works in action, let’s try to read from an environment variable on our local system. we use a system method System.getenv().

Change the main class, SampleempsApplication, to try and read in an environment variable called LAMBDA. So the main class will look like this in the end.

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
Run the application. In your IntelliJ console, you will see the value of the environment variable in upper case if it is set on your system. If it is not set, you will see the message no such environment variable. Either way the program will continue. Try working with the environment variable PATH and see what happens! Do note you get the output twice, once from the regular run of the application and once from the DevTools run of the application.

Challenge
Following the same process you used to create a ResourceNotFoundException, create a ResourceFoundException to be used in place of the built-in exception EntityExistsException.

Learn to use data validation annotations to verify data prior to processing the data

Overview
See the Github Repository https://github.com/LambdaSchool/java-exceptionalsampleemps.git for the code used in the objective.
Software Needed
Java Development Kit (JDK) - at least version 11
JetBrains IntelliJ IDEA IDE
Postman
We as backend developers are responsible for the integrity of the data in our systems. Clients will send up garbage at times. That might be due to a system problem or some initial act to sabotage our database. We cannot rely on the data we receive to be valid. We must first validate the data before it gets saved and used in our system.

Did the client send up a String when we needed an integer?
Did the client send us duplicates of a field that must be unique?
Did the client send us a poorly formatted email address?
And the list goes on.
Do NOT rely on clients to send you reasonable data. Data validation is our responsibility. I think I have said that enough but know I have many horror stories of “interesting” data from clients. Validate your own data! We have been doing this all along. We just did not label it as data validation. For example

Java is strongly typed. If we expect an integer and get a String, Java itself will cause an exception, and we know how to handle exceptions!
We use the @Column annotation to say if a column is required or must be unique across records.
We use setters to convert or validate our data before it goes to the database.
So, much data validation we get for “free” with Java Spring. Data validation is baked into the language and framework. However, certain annotations are available for use to further validate data. These are found in the import javax.validation.constraints and are put right before the fields in the models whose values we want to restrict. We can even add our own exception message using the message attribute. Some of these include:

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
@Pattern(value = "regex") allows you specific a regex pattern to use to validate the data! Discussing regex patterns are beyond the scope of this module. See additional resources for more information.
Follow Along
If you haven’t already, open the application exceptionalsampleemps you worked on in the previous objectives on Exception Handling. In the Employee Model, right before the salary field, add the appropriate annotations for a minimum salary of 100,000 and a maximum salary of 120,000. So your code will look something like this. Remember in adding all of this code, you will need to make sure the proper imports are done!

// ...

    @Transient
    public boolean hasvalueforsalary = false;

    @DecimalMax(value = "120000.0")
    @DecimalMin(value = "100000.0")
    private double salary;

// ...
Now run your application. In your console window you will see an error message similar to the following.

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
    at org.hibernate.cfg.beanvalidation.BeanValidationEventListener.onPreInsert(BeanValidationEventListener.java:80) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.action.internal.EntityInsertAction.preInsert(EntityInsertAction.java:211) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.action.internal.EntityInsertAction.execute(EntityInsertAction.java:84) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:604) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.engine.spi.ActionQueue.lambda$executeActions$1(ActionQueue.java:478) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at java.base/java.util.LinkedHashMap.forEach(LinkedHashMap.java:684) ~[na:na]
    at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:475) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.event.internal.AbstractFlushingEventListener.performExecutions(AbstractFlushingEventListener.java:348) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.event.internal.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:40) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:108) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.internal.SessionImpl.doFlush(SessionImpl.java:1344) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.internal.SessionImpl.managedFlush(SessionImpl.java:435) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.internal.SessionImpl.flushBeforeTransactionCompletion(SessionImpl.java:3221) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.internal.SessionImpl.beforeTransactionCompletion(SessionImpl.java:2389) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.engine.jdbc.internal.JdbcCoordinatorImpl.beforeTransactionCompletion(JdbcCoordinatorImpl.java:447) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.beforeCompletionCallback(JdbcResourceLocalTransactionCoordinatorImpl.java:183) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.access$300(JdbcResourceLocalTransactionCoordinatorImpl.java:40) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl$TransactionDriverControlImpl.commit(JdbcResourceLocalTransactionCoordinatorImpl.java:281) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    at org.hibernate.engine.transaction.internal.TransactionImpl.commit(TransactionImpl.java:101) ~[hibernate-core-5.4.9.Final.jar:5.4.9.Final]
    ... 21 common frames omitted
These annotations are a great way to further validate your data!

Challenge
Add an annotation to validate the email field in the Email model. Note that since the word email is already in use in the method, IntelliJ adds the full address of the annotation to give you something like @javax.validation.constraints.Email. It happens when these common words are used. We have to know which one to use at what time!
