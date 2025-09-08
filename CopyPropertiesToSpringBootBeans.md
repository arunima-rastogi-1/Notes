Suppose you have beans annotated with @MyCustomAnnotation, and you want to copy all annotation properties to bean fields.


Here is an example of how you can copy properties from an annotation to all Spring Boot beans of a given type in your application context.
This uses your provided utility method copyPropertiesToBean, iterates through beans of a type, and copies annotation properties to each bean.


# Step 1: BeanPropertiesCopier

```
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.stereotype.Component;
import org.springframework.util.StringValueResolver;

import java.lang.annotation.Annotation;
import java.util.HashSet;
import java.util.Set;

@Component
public class BeanPropertiesCopier implements BeanPostProcessor {

    @Autowired
    private ConfigurableListableBeanFactory beanFactory;

    // Example: Annotation to copy
    private final Class<? extends Annotation> annotationType = MyCustomAnnotation.class;

    // Optionally inject a StringValueResolver
    @Autowired(required = false)
    private StringValueResolver valueResolver;

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        // Check if bean class is annotated with the annotation
        Annotation ann = bean.getClass().getAnnotation(annotationType);
        if (ann != null) {
            // Exclude no properties
            copyPropertiesToBean(ann, bean, valueResolver);
        }
        return bean;
    }

    // Utility method
    public static void copyPropertiesToBean(Annotation ann, Object bean, StringValueResolver valueResolver, String... excludedProperties) {
        Set<String> excluded = new HashSet<>(Set.of(excludedProperties));
        Method[] annotationProperties = ann.annotationType().getDeclaredMethods();
        BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(bean);
        for (Method annotationProperty : annotationProperties) {
            String propertyName = annotationProperty.getName();
            if (!excluded.contains(propertyName) && bw.isWritableProperty(propertyName)) {
                Object value = ReflectionUtils.invokeMethod(annotationProperty, ann);
                if (valueResolver != null && value instanceof String) {
                    value = valueResolver.resolveStringValue((String) value);
                }
                bw.setPropertyValue(propertyName, value);
            }
        }
    }
}
```


# Usage Example

Suppose you have beans annotated with @MyCustomAnnotation, and you want to copy all annotation properties to bean fields.

````
import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyCustomAnnotation {
    String name();
    int timeout() default 1000;
}
```


```
@MyCustomAnnotation(name = "exampleBean", timeout = 5000)
public class MyBean {
    private String name;
    private int timeout;
    // getters and setters
}
```

When Spring creates beans, the BeanPropertiesCopier will copy the annotation values (name, timeout) to the bean's properties.

Summary
Use a BeanPostProcessor to apply your utility during bean initialization.
Annotation properties are copied to matching bean properties (if they exist and are writable).
Optionally exclude certain properties.





